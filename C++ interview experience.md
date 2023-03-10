# C++ interview experience

## C语言基础 & 数据结构
- 实现atoi函数，实现内存拷贝函数memcpy，字符串拷贝函数strcpy
- 反转单链表，判断链表是否存在环
- 删除单链表的某个节点C++ interview experience
- 位操作，取余
- 树的概念
>根节点、叶子节点、父节点、子节点、兄弟节点，节点的高度、深度、层数，树的高度<br>
满二叉树：叶子节点全都在最底层，除了叶子节点之外，每个节点都有左右两个子节点<br>
二叉查找树：左子节点比当前大，右子节点比当前小<br>
完全二叉树：叶子节点都在最底下两层，最后一层的叶子节点都靠左排列，除最后一层外其它的节点个数都要达到最大<br>
平衡二叉树：二叉树中任意一个节点的左右子树的高度相差不能大于1（完全二叉树、满二叉树其实都是平衡二叉树）<br>
平衡二叉查找树：二叉查找树+平衡二叉树（AVL树属于平衡二叉查找树）<br>
（发明平衡二叉查找树这类数据结构的初衷是，解决普通二叉查找树在频繁的插入、删除等动态更新的情况下，出现时间复杂度退化的问题）
AVL和RBTree的区别：维持平衡的策略不同，插入、删除效率
- 红黑树特点
- 二叉树的存储方式，为什么完全二叉树最后一层的叶子节点要靠左排列
>存储方式：链表存储、数组存储<br>
靠左排列：用数组存储完全二叉树比较节省空间（根节点1,父节点i/2,当前节点i,左子节点2i,右子节点2i+1）
- 说一下快速排序、堆排序、插入排序...
- 堆排序利用堆(heap)这种数据结构的特性进行排序，时间复杂度O(nlogn)，且是原地排序（空间复杂度低）
- 堆有两个特点：<br>
>堆是一个完全二叉树（除了最后一层，其他层的节点个数都是满的，最后一层的节点都靠左排列）<br>
>堆中每一个节点的值都必须大于等于（或小于等于）其子树中每个节点的值<br>
- 利用堆的两个操作（插入一个数据、删除堆顶元素）可以实现排序功能（key:堆顶一顶是最大或最小的）
>过程：建堆-排序（迭代地将堆顶的元素放到堆的末尾）<br>
>插入一个数据的时候，把新插入的数据放到数组的最后，然后从下往上堆化<br>
>删除堆顶数据的时候，把数组中的最后一个元素放到堆顶，然后从上往下堆化<br>

## C++基础

- 对虚函数的理解？它的实现原理？
>虚函数的作用是实现多态，调用虚函数实际调用的是创建该对象的具体类型
g++和VC++都通过虚函数表实现它，虚函数表在编译期确定，位于对象的内存头部，可以通过对象首地址访问到，
虚函数表中存放了所有虚函数，虚函数表在编译时创建，构造时__vfptr指向实际创建它的基类或子类虚函数表

- 构造函数可以为虚函数吗，为什么？
>构造函数在对象创建的时候自动被调用，而虚函数的实际地址保存在虚函数表中，此时对象的内存空间还没有构造好；
构造函数的作用是初始化实例（this），它作为虚函数实现多态（将实际调用子类函数）没有意义

- 构造函数可以重载吗？
>可以，通过不同的参数构造出不同属性的对象

- 介绍下std::vector的扩容机制

- 三种时间复杂度是O(n)的排序算法
>桶排序、计数排序、基数排序

- std::map和std::unorder_map区别
>有序-VS-无序
红黑树-VS-哈希表
占内存少-VS-占内存多
查找、插入、删除都很快-VS-适用场景广、查找快

- boost了解哪些（第三方库的了解）
> 日期时间date_time<br>
> 字符串处理format<br>
> 正则表达式xpresssive<br>
> 数据结构variant,bitmap<br>
> 文件系统filesystem<br>
> 智能指针scoped_ptr,shared_ptr,weak_ptr（shared_ptr智能指针的实现原理）<br>
> 多线程std::asio,std::thread<br>

## 面向对象
- 对面向对象的理解？
>面向对象编程是一种编程范式或编程风格。它以类或对象作为组织代码的基本单元，用类表示概念，
并将封装、抽象、继承、多态四个特性，作为代码设计和实现的基石

- 几大OO设计原则
>SOLID（单一职责原则、开闭原则、里氏替换原则、接口隔离原则、依赖倒置原则）、KISS、DRY、YAGNI、LOD

- 你的项目的代码是怎么组织起来的（划分模块）？
>"1+X" 基础库（几何计算+接口+内存管理+日志+加密等）+业务软件，分层架构：<br>
PmBase（常量、定义，数据库访问，数学计算相关Lib，工程、软件启动、加密检测，公共函数）<br>
PmCadBase（启动引擎，图形计算函数，自定义实体基类，平台相关操作，自定义工具类）<br>
PmAz（数据库访问，自定义实体，软件核心功能逻辑，工程量计算、报表，软件功能界面，提取）

- [分层架构](https://zhuanlan.zhihu.com/p/40353581)
- 通过层(Layer)来隔离不同的关注点（Concern Point），以此应对不同需求的变化，使得这种变化可以独立进行
- 经典三层架构：数据访问层、业务逻辑层、用户界面层
- 领域驱动设计中的分层架构：基础设施层、领域层、应用层、用户界面层
在用户界面层与业务逻辑层之间引入了新的一层，即应用层（Application Layer）。将业务逻辑层更名为领域层自然是题中应有之义，而将数据访问层更名为基础设施层（Infrastructure Layer），则突破了之前数据库管理系统的限制，扩大了这个负责封装技术复杂度的基础层次的内涵
- 被滥用的原因：
当分层架构变得越来越普及时，我们的设计反而变得越来越僵化。一部分软件设计师并未理解分层架构的本质，只知道依样画葫芦地将分层应用到系统中。要么采用经典的三层架构，要么遵循领域驱动设计改进的四层架构，却未思考和叩问如此分层究竟有何道理？这是分层架构被滥用的根源

## 其它
- 计算机内存结构（内存管理、大内存问题）
- 动态链接和静态链接
- 进程和线程的区别
- 排列组合、概率、线性代数等
- 求点(a,b)到线上的最近点
>基础：互相垂直的两直线的斜率相乘为-1<br>
1.通过已知直线求出与它垂直且过点(a,b)的垂线的斜率<br>
2.通过点(a,b)和斜率求出垂线方程<br>
3.联立两个方程（二元一次），求出垂足（它既在已知直线上也在垂线上，即最近点）

#### 自我介绍
- 面试官你好，我叫XXX，毕业至今工作六年了，在第一家公司工作了一年，第二家工作了四年，第三家工作了一年。<br>
- 三份工作经历都是和几何图形相关的，五年时间经历了从职场小白到核心业务模块的负责人，再到从0到1地开发完整的商业软件，再到小团队项目经理的过程。<br>
- 技术上的话，对C++非常熟悉，C++水平在团队内是数一数二的，日常工作中还经常用到的技术比如数据读写（数据库SQLite、日志、xml、excel）、界面开发（MFC、Duilib、QT）、图形计算（点、线、面、向量、矩阵，拉伸、缩放）。<br>
- 项目中做过性能优化，开源过大规模的图形求交算法，并应用到多个软件的各种业务场景中。<br>
- 最近一份工作中还做过内存优化，通过copy-on-write和享元模式的方法来节省内存。
- 我最擅长的是大型软件的架构设计工作，在重构方面的经验、心得比较多。
- 最后我有一定的管理经验，比较有大局观，在做计划和执行、反馈上做得还不错，平时经常看一些和项目相关的开源技术，比如purecpp、github等。
