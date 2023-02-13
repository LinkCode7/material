# C++语言层面的性能陷阱

### 虚函数
1. 虚函数调用带来的成本
2. 会多一次寻址操作，去虚函数表查询函数的地址
3. 可能会破坏cpu流水线，因为虚函数调用是一次间接调用，需要进行分支预测
4. 阻碍了编译器内联，大部分情况下，虚函数是无法被内联的（与前两条相比，无法内联才是虚函数性能损耗的主要来源）

### 隐式拷贝
#### 构造函数
```cpp
class C {
    A a_;
    B b_;
 public:
  C(A a, B b): a_(a), b_(b) {}
};

int main() {
  A a;
  B b;
  C c(a, b);
}

// 如果A、B是非平凡的类，会各被复制两次，在传入构造函数时一次，在构造时一次。C的构造函数应当改为：
// C(A a, B b): a_(std::move(a)), b_(std::move(b)){}
```

#### for循环
```cpp
std::vector<std::string> vec;
for (std::string str: vec){
    // ...
}
// 这里每个string会被复制一次，可改为for(const std::string& str: vec)，最好是for (const auto& str: vec)
```

#### lambda捕获
```cpp
A a;
auto f = [a]{}; // 默认值捕获
// 可以根据需求考虑使用引用捕获auto f= [&a]{};
// 或者用std::move捕获初始化auto f= [a = std::move(a)]{};
```

#### 隐式类型转换
```cpp
std::unordered_map<int, std::string> map;
for(const std::pair<int, std::string>& p: map) { // 类型错误，发生隐式拷贝
    // ...
}
// unordered_map 的元素类型是using value_type = pair<const _Kty, _Ty>;
// 所以在遍历时，推荐使用const auto&，对于map类型，也可以使用结构化绑定
```

### 隐式析构
#### 平凡的析构类型
```cpp
class A {
public:
    int l, r;
    ~A() {}; // 自定义析构函数，会导致类为不可平凡析构类型(std::is_trivially_destructible)和不可平凡复制类型
};
A get() {
    return A{41, 42};
}
/*
get():                                # @get()
        movq    %rdi, %rax
        movabsq $180388626473, %rcx             # imm = 0x2A00000029
        movq    %rcx, (%rdi)
        retq
*/

class A2 {
public:
    int l, r;
    ~A2() = default;
};
A2 get2() {
    return A2{41, 42};
}
/*
get():                                # @get()
        movabsq $180388626473, %rax             # imm = 0x2A00000029
        retq
*/
```

#### 滥用std::shared_ptr
C++核心指南是这样推荐智能指针的用法的：
- 用 std::unique_ptr或 std::shared_ptr表达资源的所有权。
- 不涉及所有权时，用裸指针。
- 尽量使用std::unique_ptr，只有当资源需要被共享所有权时，再用std::shared_ptr。

必须用std::shared_ptr的场景有：异步析构，缓存。
std::shared_ptr确实是懒人的福音，既保证了资源的安全，又不用梳理资源的所有权模型。
实际上，std::shared_ptr的构造、复制和析构都是非常重的操作，因为涉及到原子操作，std::shared_ptr是要比裸指针和std::unique_ptr慢10%～20%的。
即使用了std::shared_ptr也要使用std::move和引用等等，尽量避免拷贝。
std::shared_ptr一定要用std::make_shared<T>()而不是std::shared_ptr<T>(new T)来构造，后者会分配两次内存，且原子计数和数据本身的内存是不挨着的，不利于cpu缓存。

#### 类型擦除：std::function和std::any
std::function，可以封装任何可被调用的对象，包括常规函数、类的成员函数、有operator()定义的类、lambda函数等，但std::function是有成本的：
- std::function要占用32个字节，而函数指针只需要8个字节。
- std::function本质上是一个虚函数调用，因此虚函数的问题std::function都有，比如无法内联。
- std::function可能涉及堆内存分配，比如lambda捕获了大量值时，用std::function封装会需要在堆上分配内存。
- 因此我们只应在必须时才使用std::function，比如需要存储一个不确定类型的函数。
- 在只需要多态调用的，完全可以用模版静态派发，std::any同理，用类型擦除的机制可以存储任何类型，但是也不推荐使用。
```cpp
template <typename Func>
void Run(Func&& f){
    f();
}
```

#### std::variant和std::optional
- 必须的多余内存开销：简单来说，std::optional有两个成员变量，类型分别为bool和T，由于内存对齐的原因，sizeof(std::optional)会是sizeof(T)的两倍。
相比之下，rust语言的option实现则有null pointer optimization，即如果一个类的合法内存表示一定不会全部字节为零，
比如std::reference_wrapper，那就可以零开销地表示std::optional，而C++由于需要兼容C的内存对齐，不可能实现这项优化。
- c++标准要求如果T是可平凡析构的（见上文析构的部分），std::optional也必须是平凡析构的，但是gcc在8.0.0之前的实现是有bug的，
所有std::optional都被设置为了非平凡类型，所以用std::optional作为工厂函数的返回值是由额外性能开销的。
- 对NRVO（返回值优化）不友好，见下文NRVO的部分。

