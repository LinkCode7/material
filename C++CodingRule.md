# C++编码规范

## 一般性原则

### 遵守尽可能减少意外性的原则
>编码流程化，认真仔细地考虑各种情况，并提供应对
### 最初即完成
>原型或实验性编码的一部分进入到最终代码，即使代码最终不会成为产品，但其他人可能因为阅读此代码而增加了负担。若编码人员会从一开始就一贯地适用这些规则，则评审人员会高度评价作者的专业意识和预见性。
### 脱离规则
>没有完美的标准，也没有普遍适用的标准，有时也会需要脱离确立的标准。在决定无视规则之前，你首先必须确认理解规则存在的理由和不应用规则时的结果。在此基础上，如果你觉得有必要违反规定，那就把理由记录下来。
1. 经常有充分的理由而偏离规则
2. 编码标准的目的是帮助能够沟通的队伍
3. 编码规范应根据通用的标准制定，是团队共识的结晶，不是个人意志的体现

## 命名规则
>命名要表达清晰、明确的含义，要有描述性  
>少用缩写，特别是不常用、不直观的缩写，附：[常用缩写](#abbreviation)

- 文件名用大小写区分，增加可读性

- 类名以公司简称开头，防止命名冲突，标识代码层级

- 以小写字母开头，以大写字母分割单词，类名和函数名
```cpp
void getName();
```

- 成员变量加前缀m_，注意：struct也要加

- 静态变量、全局变量要加前缀s_或g_

- 命名方式动词+名词、名词或者形容词＋名词、动宾结构
```cpp
void setAddress(const Address& address);
```

- 函数名要准确描述函数的功能

- 函数内的变量、参数用小写字母开头的单词组合而成，可以以类型为前缀

- 返回bool的函数命名：is + 形容詞，can + 动词，has + 过去分词
```cpp
bool isEmpty();
bool empty(); // 应理解为动词，清空
bool canGet();
bool hasChanged();
bool isContains(Object);
bool containsKey(Key);

if (Object.isContains(obj)) // 在if、while等语句中更容易阅读（更连贯）
```

- 注意命名的对称性
>
add/remove  
insert/delete  
get/set  
begin/end  
***start/stop(start不对应end)***  
from/to  
send/receive  
first/last  
get/release  
put/get  
up/down  
show/hide  
source/target  
open/close  
source/destination  
src/dest  
increment/decrement  
lock/unlock  
old/new  
next/previous  
read/write  

- 变量声明类型放前，定义如typedef类型在后

- 智能指针的定义以Sp/Wp/Up结尾
```cpp
using DatasetSp = std::shared_ptr<Dataset>; // 统一标识、简洁明了
```

- 禁止滥用省略名称
```cpp
double temp = sqrt(X); // 错误示例：滥用命名temp，没有含义
```
- 省略形式的单词以大写开头，后续小写
>class XMLString → class XmlString  
loadXMLDocument() → loadXmlDocument()

- 局部变量可以使用常见易懂的缩写，但特殊情况仍需注释
```cpp
Polyline3d poly;
ServletContext sc = getServletContext(); // sc是狭长的类型名缩写
```

- 宏定义用大写字母和下划线命名
```cpp
#define RETURN_MAX(a, b) ((a) > (b) ? (a) : (b)) // 较好的示例
#define GOLDENSECTION 0.618 // 错误示例，应该是GOLDEN_SECTION
```

- 宏定义尽量短，展开后是多行的，最好改为函数（无法调试）

- 枚举类型尽量不要写具体数值（很多情况下数值没有含义），除非需要序列化（此时应备注该枚举的数值已固定）
```cpp
enum class GeometryType
{
    kUnknown, // 未知状态尽量设为0
    kLine, // 如果这里写了具体数值，那么维护者就要小心翼翼地考虑它们大小、顺序
    kCircle,
    kRectangle
}
```

- 不希望被调用的内部实现函数，以下划线开头
```cpp
class Segment3d
{
public:
    void move(const Vector3d& vct); // move函数内调用_move，将两个强相关的函数放到一起方便维护
    void _move(const Matrix3d& mat); // 不希望将_move放到private中，又不希望它被外部调用
    // ...
private:
    // ...
}
```

- 单数/复数形式代表不同含义，如：Point/Points

## 注释
>自动生成开发文档工具：Doxygen
- 注释的书写应遵循简洁明了的原则，目的是增强软件的可维护性

- 函数的概括性注释写在头文件中，函数的细节性注释写在源文件的相应位置

- 注释需要维护，代码变更后应一并修改相关注释，保证后期的可读性

## 编码
### 类
- 类的职责要明确、单一
>一个文件内最好只有一个类，小的数据类不必单独占一个文件

- 类的函数放在对应的cpp文件，不要分散在不同文件（便于查找，加快链接速度）

- 如果类中定义了成员变量，请尽可能地提供构造函数
>若没有提供构造函数，编译器会默认生成，极有可能使成员变量未能正确初始化，进而引发不确定的后果

- 将类的所有数据成员声明为private，提供get/set方法访问它们，尽量避免用struct而统一用class关键字
>应禁止class中公开的成员变量，这么做可以集中控制访问点、修改点，更符合OOP的封装特性
>提供对外"接口"时，保证不会破坏内部数据的整合性

- 使用多态替代分支判断
>当遇到两处以上的switch或if/else时，就要考虑使用多态  
>使用多态时，应从设计的角度考虑代码块之间的关系

- 使用到了继承，就要使用虚析构函数
>防止释放基类指针时，子类申请的内存泄漏  
>如果子类没有申请内存，那么不存在泄露问题，但是仍然可以加上virtual

- 通过类的构造函数初始化对象，析构函数释放对象
>避免使用无参构造函数初始化对象后，紧接着通过其它方式初始化成员变量（此时应该叫赋值）  
>避免在子类的构造函数体内对基类数据进行赋值  
>>小心基类的构造、析构陷阱：  
>>避免在基类的构造函数内调用虚函数，此时子类还没有构造出来  
>>避免在基类的析构函数内调用虚函数，此时子类已析构

- 重写基类的虚函数时，请使用override关键字表明重写意图

### 函数
- 避免写大而全的函数，一个函数只做一件事

- 尽量限制一个函数的行数在200以内，最好在一个视口内能显示全

- 单行代码不要超过100字，按逻辑分割

- 函数中的代码要尽早返回，减少大括号划分的代码层次
```cpp
// 错误示例，括号层级太深，阅读困难
void getEntityDoSomething(std::shared_ptr<Database> pDb, int id)
{
	std::shared_ptr<BlockTable> pBlockTable;
	if (pDb && pDb->getSymbolTable(pBlockTable, kForRead))
	{
		std::shared_ptr<BlockTableRecord> pBlockTableRecord;
		if (pBlockTable->getAt(MODEL_SPACE, pBlockTableRecord, kForRead))
		{
			std::shared_ptr<BlockTableRecordIterator> pIterator;
			pBlockTableRecord->newIterator(pIterator);

			for (; !pIterator->done(); pIterator->step())
			{
				std::shared_ptr<Entity> pEntity;
				if (pIterator->getEntity(pEntity, kForRead))
				{
					if (pEntity->isKindOf(MyEntity::desc()))
					{
						if (dynamic_pointer_cast<MyEntity>(pEntity) == id)
						{
							// ... // 核心逻辑位于函数的第七级，可读性较差
						}
					}
					// ...
				}
			}
		}
	}
}
// 较好的示例，异常条件提前返回了，降低了关注度
void getEntityDoSomething2(std::shared_ptr<Database> pDb, int id)
{
	if (!pDb)
		return;

	std::shared_ptr<BlockTable> pBlockTable;
	if (pDb->getSymbolTable(pBlockTable, kForRead))
		return;

	std::shared_ptr<BlockTableRecord> pBlockTableRecord;
	if (!pBlockTable->getAt(MODEL_SPACE, pBlockTableRecord, kForRead))
		return;

	std::shared_ptr<BlockTableRecordIterator> pIterator;
	pBlockTableRecord->newIterator(pIterator);

	for (; !pIterator->done(); pIterator->step())
	{
		std::shared_ptr<Entity> pEntity;
		if (!pIterator->getEntity(pEntity, kForRead))
			continue;

		if (pEntity->isKindOf(MyEntity::desc()))
		{
			if (dynamic_pointer_cast<MyEntity>(pEntity) != id)
				continue;
			// ... // 核心逻辑位于函数的第三级，可读性较好
		}
		// ...
	}
}
```

- 内联函数应在10行以内
>只有精简的函数能形成内联  
inline关键字对析构函数、递归函数、带循环的函数无效

- 除内置类型外，在所有能用const &的地方用上"const &"
>C++11及以后的标准中，实现了移动构造的对象可以不写"const &"，不写的效率甚至更高一点点
```cpp
// “const int &count”和"int count"效果一样，为了避免函数声明过长，内置类型尽量不要写"const &"
void calculate(const std::vector<int> &arrObjectId, const std::string &strName, int count);
```
- 空指针防御由函数提供者保证
>由调用者保证有以下问题：忘记写判断、软件不稳定、代码不简洁

- 好的函数重载是参数数量不同，这样会方便阅读

- 大量的套壳函数（函数内只有一两行代码）是不优雅的

- 函数实现尽量不要放在头文件，降低编译时间

- 禁止使用goto关键字
>肆意改变程序流会增加代码阅读难度
>goto有且仅有一个好处：跳出多层循环

### 变量
- 业务逻辑中禁止使用静态变量，除非有非用不可的理由

- 小心隐式转换，不要让型别轻易转换，考虑使用constexpr

- 成员变量的初始化，尽可能在初始化列表中完成
>构造函数内"初始化"成员变量，其实是给对象赋值
>>注意：
>>执行顺序：头文件就地初始化 -> 列表初始化 -> 构造函数赋值
>>自定义对象的初始化可能涉及数据的拷贝，尽可能在初始化列表中完成
>>成员变量在初始化列表中的构造顺序，不是初始化列表中的顺序，而是class中定义的先后顺序（对象依赖）

- 不要进行没有意义的初始化，如：std::string strName = "";

- 局部变量的初始化时机，RAII(Resource Acquisition Is Initialization)
```cpp
// 错误示例
{
    int index;
    // ...
    index = start + 1;
    // 使用index
}

// 较好的示例
{
    // ...
    int index = start + 1; // 就地初始化，就近初始化
    // 使用index
}
```

- 用括号明确复杂表达式的优先级
```cpp
if (A || (B && C)) // 较好的示例
if (A || B && C) // 错误示例
```

- 递增变量时，使用前置递增，如：++i/++iterator
>不要浪费时间或空间，因为你用的是C++语言

### 其它
- 积极使用STL，绝大多数情况下标准库能够提供实现，请不要自己实现
>能使用更简洁、逻辑一致的方法名  
>通过标准收藏类的接口，可以不改变接口而更换实现  
>写出更通用、更规范、安全行更高、兼容性更强的代码

- 在编写业务逻辑时，绝大多数情况应该使用智能指针替代原生指针

- 头文件中尽量减少对其它头文件的包含，不要写using其它命名空间，如：using namespace std;
>头文件中使用的引用或指针类型，只需要声明，不用包含  
>包含这个头文件的地方也被强制使用了命名空间，容易造成名称混乱  
>降低编译依赖、减少编译时间  
```cpp
class Object
{
    vector<int> m_arrId; // 错误示例，此程序的头文件中包含了std命名空间，应该是std::vector<int> m_arrId;
}
```

- 不要使用复杂的模板编程
>泛型编程是编程范式层面的选择之一，适合大型的、底层的库开发，如boost/STL
>对于使用C++不是很熟练的人来说比较晦涩，编译信息很不友好，调试、维护也比较麻烦  
>过多的模板参数是不友好的

- 不要使用复杂的lambda表达式
>主流的编程范式仍然是面向对象，它足够成熟，有很多设计模式可复用  
>函数式编程可以封装设计模式，但它还不成熟，没有成熟的设计范式可用
>>一旦lambda表达式引用的外部变量失效，可能造成程序崩溃（野指针），且很难调试问题  
>>由于业务的复杂性，存在多个闭包嵌套的情形，此时函数的可读性极差，要了解所有上下文代码，程序流可能在底层、上层间来回穿梭

- 不完全正确、有待继续完成的、暂未实施的内容，用TODO标识

- 在编码之前与上一任作者交流
>确认做的东西在Lib中是否存在  
>要继承某个class时，向它的制作者咨询，确认是否通用的方式，这样能使整体紧凑...

- 具有迷惑性的代码是邪恶的
>写出让平均程序员明白的代码，例如运算符的顺序、关于初始化的规则等，
不带入任何人都未必能充满自信的回答的假设，使用()明确运算顺序，进行明确的初始化等
```cpp
// 较好的示例
#define SWAP_INT(a,b) { \
    int temp = a; \
    a = b;       \
    b = temp;    \
}
// 错误示例
#define SWAP_INT(a,b) {a += b -= (a = b-a);}
```

## 设计
- 设计复杂的东西是一件错误的事情
>设计上遇到困难时，多数情况应重视简洁明了（KISS原则），尝试将问题分解成简单的小块

- 用组合代替继承

## 性能
>实际项目中的性能数据要从测试中得出，不能仅靠时间复杂度分析，更不能依靠猜测  
>在测试之后做性能调整，不应该从最开始就过分介意性能问题，易读性和维护性优先，性能在测定后改善

- 在保证软件系统的正确性、稳定性、可读性及可测试性的前提下，尽可能地提高软件的性能

- 在保证程序质量的前提下，通过压缩代码量、去掉不必要代码以及减少不必要的局部和全局变量，来提高空间效率

- 减少循环嵌套层次，将变量声明放到循环外面

- 在多重循环中，应将最忙的循环放在最内层

- 避免循环体内含判断语句，应将循环语句置于判断语句的代码块之中
>循环语句不被执行

- 尽量用乘法或其它方法代替除法，特别是浮点运算中的除法

- 针对执行代码，不要在循环中做重复的事情，特别是IO操作
>重复读写文档，重复读写数据等操作

- 不要对动态数组使用removeAt、insertAt方法，除非有非用不可的理由
>这么做大概率会触发动态数组的内存搬迁

- 一般情况下，优先使用动态数组而非关联容器
>在内存不吃紧的软件中，动态数组的内存分配策略可以大大减少new的时间，map、set在每次插入、删除元素时要调整树的平衡



## 附录
### <a name="abbreviation"></a>常用缩写
>注：蓝色为常用的

| 缩写 | 全称 | 备注 |
| :-----| :---- | :---- |
<font color=#0099ff>addr</font> |Address|
adm	|Administrator
<font color=#0099ff>app</font>	|Application
<font color=#0099ff>arg</font>	|Argument	argc?argv?
asm	|assemble	弃用
asyn|asynchronization	
avg	|average	弃用
<font color=#0099ff>db</font>	|Database
bk	|back	
<font color=#0099ff>bmp</font>	|Bitmap
<font color=#0099ff>btn</font>	|Button|尽量只在控件ID命名时使用
<font color=#0099ff>buf</font>	|Buffer
<font color=#0099ff>calc</font>	|Calculate
chg	|Change	弃用
<font color=#0099ff>clk</font>	|Click
<font color=#0099ff>clr</font>	|color
<font color=#0099ff>cmd</font>	|Command
<font color=#0099ff>cmp</font>	|Compare
<font color=#0099ff>col</font>	|Column
<font color=#0099ff>coord</font>|	coordinates
<font color=#0099ff>cpy</font>	|copy
<font color=#0099ff>ctl</font>	|Control
<font color=#0099ff>cur</font>	|Current
cyl	|Cylinder
<font color=#0099ff>dbg</font>	|Debug
<font color=#0099ff>dbl</font>	|Double
dec	|Decrease
<font color=#0099ff>def</font>	|default
<font color=#0099ff>del</font>	|Delete
<font color=#0099ff>dest</font>	|Destination
dev|Device?Develop?	弃用
dict|dictionary
<font color=#0099ff>diff</font> |different
<font color=#0099ff>dir</font>	|directory
<font color=#0099ff>disp</font>	|Display	不是那么友好
div	|Divide	弃用
<font color=#0099ff>dlg</font>	|Dialog
<font color=#0099ff>doc</font>	|Document
drv	|Driver	弃用
dyna	|Dynamic	弃用
<font color=#0099ff>env</font>	|Environment
<font color=#0099ff>err</font>	|error
<font color=#0099ff>ext</font>	|Extend	|Windows的旧风格，以Ex开头的函数...建议弃用
<font color=#0099ff>exec</font>	|execute
flg	|flag	多写一个字母会更友好
frm	|Frame	弃用
<font color=#0099ff>func</font>|Function	fun
grp	|group	弃用
horz	|Horizontal	hor
idx 	|Index	弃用
<font color=#0099ff>img</font>	|Image
<font color=#0099ff>imp</font>	|Implement
inc	|Increase	弃用
<font color=#0099ff>info</font>	|Information
<font color=#0099ff>init</font>	|Initialize
ins	|Insert	弃用
inst	|Instance	弃用
intr	|Interrupt	弃用
<font color=#0099ff>len</font>	|Length
<font color=#0099ff>lib</font>	|Library
lnk	|Link	弃用
<font color=#0099ff>log</font>	|logical
lst	|List	
<font color=#0099ff>max</font>	|maximum
mem	|Memory	弃用
mgr	|Manager	弃用
<font color=#0099ff>mid</font>	|middle
<font color=#0099ff>min</font>	|minimum
<font color=#0099ff>msg</font>	|Message
<font color=#0099ff>multi</font>	|Multiply
<font color=#0099ff>num</font>	|Number
<font color=#0099ff>obj</font>	|Object
ofs	|Offset	
<font color=#0099ff>org</font>	|Origin 
<font color=#0099ff>param</font>	|Parameter
pic	|picture	弃用
pkg	|package	弃用
<font color=#0099ff>pt</font>	|Point
<font color=#0099ff>pos</font>	|Position
<font color=#0099ff>pre</font>	|previous
prg	|program	弃用
prn	|Print	弃用
<font color=#0099ff>proc</font>	|Process 
<font color=#0099ff>prop</font>	|Properties	不是那么明确
psw	|Password|不是那么明确
<font color=#0099ff>ptr</font>	|Pointer
pub	|Public	弃用
rc	|rect?作为返回值的rc?
<font color=#0099ff>ref</font>	|Reference
<font color=#0099ff>reg</font>	|Register
req	|request	
res	|Resource	
ret	|return	
rgn	|region	|不那么常用
scr	|screen	
<font color=#0099ff>sec</font>	|Second
<font color=#0099ff>seg</font>	|Segment
<font color=#0099ff>sel</font>	|Select
<font color=#0099ff>src</font>	|Source
<font color=#0099ff>std</font>	|Standard
stg	|Storage	
stm	|Stream?Statement?	弃用
<font color=#0099ff>str</font>	|String
<font color=#0099ff>sub</font>	|Subtract
<font color=#0099ff>sum</font>	|Summation
svr	|Server|弃用，不明确或不够明确
<font color=#0099ff>sync</font>	|Synchronization
<font color=#0099ff>sys</font>	|System
tbl	|Table	弃用
<font color=#0099ff>temp</font>	|Temporary
<font color=#0099ff>trans</font>	|translate?transation?transparent?
tst	|Test|弃用，不良的缩写方式，多写一个字母对阅读者更友好
<font color=#0099ff>txt</font>|text|弃用，txt更指代文件，text更指代文本
unk	|Unknown	
upd	|Update	弃用
upg	|Upgrade	弃用
<font color=#0099ff>util</font>	|Utility
<font color=#0099ff>var</font>	|Variable
ver	|Version	|不是很好
vert	|Vertical	ver,vec
vir	|Virus?Virtual?	弃用
<font color=#0099ff>wnd</font>	|Window
