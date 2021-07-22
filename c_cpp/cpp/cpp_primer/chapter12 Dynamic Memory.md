- [第十二章 动态内存](#第十二章-动态内存)
  - [一、new 和 delete](#一new-和-delete)
    - [1.1 可以为new分配的对象命名吗？](#11-可以为new分配的对象命名吗)
    - [1.2 new的返回值是什么？](#12-new的返回值是什么)
    - [1.3 使用new动态分配对象时，若没有对其进行初始化将会发生什么？](#13-使用new动态分配对象时若没有对其进行初始化将会发生什么)
    - [1.4 如何初始化new出来的对象？](#14-如何初始化new出来的对象)
    - [1.5 使用auto来new对象时要注意什么？](#15-使用auto来new对象时要注意什么)
    - [1.6 如何动态分配 const对象？](#16-如何动态分配-const对象)
    - [1.7 为什么说 new 可能造成机器的内存耗尽？](#17-为什么说-new-可能造成机器的内存耗尽)
    - [1.8 内存耗尽了的时候 使用new分配内存 会发生什么？](#18-内存耗尽了的时候-使用new分配内存-会发生什么)
    - [1.9 如何查看是否内存泄露？](#19-如何查看是否内存泄露)
    - [1.10 使用delete释放内存时要注意什么？](#110-使用delete释放内存时要注意什么)
    - [1.11 什么是 空悬指针(dangling pointer)？如何解决？](#111-什么是-空悬指针dangling-pointer如何解决)
    - [1.12 为什么说有一些动态内存除非在程序关闭时由OS回收外，永远不可能被释放？](#112-为什么说有一些动态内存除非在程序关闭时由os回收外永远不可能被释放)
    - [3.13 使用 new 和 delete 管理动态内存有哪些常见的错误？](#313-使用-new-和-delete-管理动态内存有哪些常见的错误)
  - [二、智能指针](#二智能指针)
    - [2.1 标准库定义了哪些智能指针？它们分别用于什么场景？](#21-标准库定义了哪些智能指针它们分别用于什么场景)
    - [2.2 智能指针是通过什么实现的？](#22-智能指针是通过什么实现的)
    - [2.3 使用智能指针有什么好处？](#23-使用智能指针有什么好处)
    - [2.4 智能指针 可以 托管 非动态分配的对象吗？](#24-智能指针-可以-托管-非动态分配的对象吗)
  - [三、shared_ptr类](#三shared_ptr类)
    - [3.1 如何定义一个指向 string类型(值为：Hello world) 的 shared_ptr?](#31-如何定义一个指向-string类型值为hello-world-的-shared_ptr)
    - [3.2 定义一个 shared_ptr 时，若不对其进行显式初始化，变量的值是？](#32-定义一个-shared_ptr-时若不对其进行显式初始化变量的值是)
    - [3.3 shared_ptr支持的操作](#33-shared_ptr支持的操作)
      - [3.3.1 和unique_ptr共有的操作](#331-和unique_ptr共有的操作)
      - [3.3.2 shared_ptr独有的操作](#332-shared_ptr独有的操作)
      - [3.3.3 如何判断 指向string的智能指针p指向的值是否为空 比较安全？](#333-如何判断-指向string的智能指针p指向的值是否为空-比较安全)
    - [3.4 make_shared 函数](#34-make_shared-函数)
      - [3.4.1 作用是？](#341-作用是)
      - [3.4.2 怎么用？](#342-怎么用)
      - [3.4.3 原理是？](#343-原理是)
    - [3.5 shared_ptr 的 引用计数 规则是怎样的？](#35-shared_ptr-的-引用计数-规则是怎样的)
      - [3.5.1 将一个 `shared_ptr`指针q 赋给 另一个`shared_ptr`指针r 会发生什么？](#351-将一个-shared_ptr指针q-赋给-另一个shared_ptr指针r-会发生什么)
    - [3.6 shared_ptr 通过什么控制 管理的对象的 初始化 和 销毁？](#36-shared_ptr-通过什么控制-管理的对象的-初始化-和-销毁)
    - [3.7 使用 `shared_ptr` 就一定不会内存泄露了吗？](#37-使用-shared_ptr-就一定不会内存泄露了吗)
    - [3.8 程序一般出于什么原因一定要使用容易造成 内存泄露的 动态内存？](#38-程序一般出于什么原因一定要使用容易造成-内存泄露的-动态内存)
    - [3.9 定义和改变shared_ptr的其它方法](#39-定义和改变shared_ptr的其它方法)
    - [3.10 使用new出来的指针初始化 shared_ptr时要注意什么？为什么？](#310-使用new出来的指针初始化-shared_ptr时要注意什么为什么)
    - [3.10.1 具体分析](#3101-具体分析)
    - [3.11 混用 普通指针 和 智能指针有何风险？](#311-混用-普通指针-和-智能指针有何风险)
    - [3.12 为什么推荐make_shared函数，而不是new运算符？](#312-为什么推荐make_shared函数而不是new运算符)
    - [3.13 `shared_ptr`的 get函数](#313-shared_ptr的-get函数)
      - [3.13.1 get函数的作用？](#3131-get函数的作用)
      - [3.13.2 为什么要定义 get函数？](#3132-为什么要定义-get函数)
      - [3.13.3 使用建议](#3133-使用建议)
    - [3.14 `shared_ptr`的 reset函数](#314-shared_ptr的-reset函数)
  - [3.15 动态内存 和 异常](#315-动态内存-和-异常)
    - [3.15.1 为什么说异常 时很可能导致内存泄露？](#3151-为什么说异常-时很可能导致内存泄露)
    - [3.15.2 如何保证 异常发生的时候 资源能被正常释放？](#3152-如何保证-异常发生的时候-资源能被正常释放)
      - [(1) 捕获异常，在异常处理模块里将资源释放](#1-捕获异常在异常处理模块里将资源释放)
      - [(2) 使用智能指针](#2-使用智能指针)
    - [3.16 定义自己的释放操作](#316-定义自己的释放操作)
      - [一般什么情况下需要定义自己的释放操作？](#一般什么情况下需要定义自己的释放操作)
      - [3.16.2 下面的代码有什么问题？应该怎么解决？](#3162-下面的代码有什么问题应该怎么解决)
    - [3.17 智能指针陷阱](#317-智能指针陷阱)
  - [四、 unique_ptr](#四-unique_ptr)
    - [4.1 `unique_ptr` 和 `shared_ptr` 有何区别？](#41-unique_ptr-和-shared_ptr-有何区别)
    - [4.2 有无 `std::make_unique`？](#42-有无-stdmake_unique)
    - [4.3 如何新建 `unique_ptr`？](#43-如何新建-unique_ptr)
    - [4.4 unique_ptr的操作](#44-unique_ptr的操作)
    - [4.5 如何用 一个 unique_ptr 给 另一个unique_ptr赋值？](#45-如何用-一个-unique_ptr-给-另一个unique_ptr赋值)
    - [4.6 既然 `unique_ptr` 不能被复制和拷贝，我们如何返回一个 `unique_ptr`对象呢？](#46-既然-unique_ptr-不能被复制和拷贝我们如何返回一个-unique_ptr对象呢)
    - [4.7 如何给 `unique_ptr` 自定义 删除器？](#47-如何给-unique_ptr-自定义-删除器)
  - [五、 weak_ptr](#五-weak_ptr)
    - [5.1 weak_ptr 有何特点？](#51-weak_ptr-有何特点)
    - [5.2 为什么要引入 weak_ptr ？](#52-为什么要引入-weak_ptr-)
    - [5.3 weak_ptr 的操作](#53-weak_ptr-的操作)
    - [5.4 如何通过 weak_ptr 访问对象？](#54-如何通过-weak_ptr-访问对象)
  - [六、 动态数组](#六-动态数组)
    - [6.1 为什么需要动态定义数组？](#61-为什么需要动态定义数组)
    - [6.2 为什么说 动态数组 不是 数组？](#62-为什么说-动态数组-不是-数组)
    - [6.3 动态数组 的 初始化](#63-动态数组-的-初始化)
    - [6.4 创建动态数组的时候，如果 大小指定为0 会发生什么？](#64-创建动态数组的时候如果-大小指定为0-会发生什么)
    - [6.5 释放动态数组](#65-释放动态数组)
      - [&emsp;6.5.1 如何释放？](#651-如何释放)
      - [&emsp;6.5.2 动态数组的释放顺序是怎样的？](#652-动态数组的释放顺序是怎样的)
      - [&emsp;6.5.3 释放 动态数组 时，如果忘了加 `[]`会发生什么？](#653-释放-动态数组-时如果忘了加-会发生什么)
    - [6.6 可以 智能指针 管理 动态数组吗？](#66-可以-智能指针-管理-动态数组吗)
      - [&emsp;6.6.1 使用 `unique_ptr`来管理](#661-使用-unique_ptr来管理)
      - [&emsp;6.6.2 使用 `shared_ptr`来管理](#662-使用-shared_ptr来管理)
      - [&emsp;6.6.3 用`shared_ptr`管理动态数组时，若没有为提供删除起会发生什么？](#663-用shared_ptr管理动态数组时若没有为提供删除起会发生什么)
      - [&emsp;6.6.4 使用智能指针管理动态数组的总结](#664-使用智能指针管理动态数组的总结)
  - [七、 allocator类](#七-allocator类)
    - [7.1 为什么需要allocator类](#71-为什么需要allocator类)
    - [7.2 allocator类 是用什么实现的？](#72-allocator类-是用什么实现的)
    - [7.3 allocator类 定在哪个头文件？](#73-allocator类-定在哪个头文件)
    - [7.4 allocator类 支持哪些操作？](#74-allocator类-支持哪些操作)
    - [7.5 如何使用 allocator类？](#75-如何使用-allocator类)
    - [7.6 allocator类中 拷贝和填充 未初始化内存的算法](#76-allocator类中-拷贝和填充-未初始化内存的算法)
    - [7.7 `allocator::construct`的构造原理是什么？](#77-allocatorconstruct的构造原理是什么)
  - [8. `shared_ptr`、`unique_ptr`分别在哪个头文件？](#8-shared_ptrunique_ptr分别在哪个头文件)
  - [文本查询程序](#文本查询程序)
# 第十二章 动态内存

## 一、new 和 delete
### 1.1 可以为new分配的对象命名吗？
&emsp;&emsp;不能，因为在堆上分配的内存是无名的，因此new无法为期分配的对象命名。
### 1.2 new的返回值是什么？
&emsp;&emsp;new返回的是 一个指向动态分配对象的指针：
```cpp
int *pi = new int(3); 
```
### 1.3 使用new动态分配对象时，若没有对其进行初始化将会发生什么？
&emsp;&emsp;默认情况下，动态分配的对象时默认初始化的，这意味着：
>&emsp;① 内置类型 的对象的值将是未定义的；
>&emsp;② 类类型 的对象将调用其默认构造函数进行初始化
```cpp
int *pi = new int;          // int是内置类型，因此pi指向一个未初始化的int
string *ps = new string;    // string是类，将调用其默认构造函数进行初始化，因此ps为空string
```
### 1.4 如何初始化new出来的对象？
&emsp;&emsp; 可以使用 直接初始化、传统的构造方式(用圆括号)、列表初始化（花括号，C++11标准）：
```cpp
int *pi = new int(1024);    // 直接初始化
vector<int> * pvec = new vector<int>{1, 2, 3, 4, 5, 6}; // 列表初始化
```
也可以使用 值初始化（在类型名后面根一对空的圆括号即可）：
```cpp
string *ps2 = new string;       // 默认初始化为空string
string *ps2 = new string();     // 值初始化为空string

int * pi1 = new int;    // 默认初始化，*pi1的值是未定义的
int * pi2 = new int();  // 值初始化为0
```
**注意：**
>① 对于类类型，是否通过圆括号来要求编译器对其进行值初始化没有什么区别，因为不管不怎么样编译器都会调用其默认构造函数来对其进行初始化；
② 但对于内置类型就不一样了，如果 不通过圆括号来要求编译器对其进行值初始，那么它将进行默认初始化，也就是说它的值将是未定义的，但值初始化就不一样了，对象将有着定义良好的值；
③ 同理，对于依赖编译器合成默认构造函数的类，里面的内置成员也将执行默认初始化，即它们的值将是未定义的。
### 1.5 使用auto来new对象时要注意什么？
① 只有括号中仅含单一的初始化器时才能使用auto；
② 将用初始化器里的值来初始化new出来的对象
③ auto和new配合使用时返回的是指针；
```cpp
int obj = 0;
// pi是指向obj类型的指针
// pi指向的对象将用obj的值来初始化
auto p1 = new auto(obj); 

// 使用auto推断new出来的对象时，初始化器中只能有单一的值
auto p2 = new auto{a, b, c};
```
### 1.6 如何动态分配 const对象？
&emsp;通过new分配const对象是应该注意：
&emsp;&emsp; (1) 和其它const对象一样，一个动态分配的const对象必须进行初始化;
&emsp;&emsp; (2) 通过new分配的const对象 返回的是 一个指向const的指针
```cpp
const int * pci = new const int; // 错误，必须进行初始化
const int * pci = new const int(1024);

const string * pcs = new const string; // 正确，string类有默认构造函数，psc将隐式的被初始化为一个空string
```
**注意**：上面的`pcs`变量虽然没有显示初始化，但 string类  定义了默认构造函数，因此将通过默认构造函数进行隐式初始化，所以没有错。
### 1.7 为什么说 new 可能造成机器的内存耗尽？
&emsp;&emsp; new 出来的内存如果不在使用完后及时释放的话，将一直存在，虽然现在很多计算机内存都很大，但是时间长了还是会积少成多，最后耗尽内存。
### 1.8 内存耗尽了的时候 使用new分配内存 会发生什么？
&emsp;&emsp; 内存耗尽了就意味着就没有内存可以分配了，此时若使用new分配内存将抛出一个`bad_alloc`异常，但我们可以改变使用 new 的方式来阻止它抛异常：
```cpp
int * p1 = new int; // 若此时内存已耗尽，则new将抛出 std::bad_alloc 异常
int * p2 = new (nothrow) int; // 若分配失败，则new将返回一个空指针
```
这种形式的new被称为：**定位new(placement new)**，原因在19.1.2节讲。
**注意**：`bad_alloc` 和 `nothrow` 都定义在 头文件new 中。
### 1.9 如何查看是否内存泄露？
(1) 通过工具：
&emsp;&emsp; linux端工具：Valgrind
&emsp;&emsp; windows工具：Visual Leak Detector
(2) 通过日志：
&emsp;&emsp; 将new和delete分别封装为函数，专门用于分配内存和释放内存，并在函数中记录 所分配内存的地址和长度、释放内存的地址和长度，等到停服务的时候看一下能不能匹配上就能定位内存泄露的代码了。
### 1.10 使用delete释放内存时要注意什么？
&emsp;&emsp; **(1)** delete 接收的是 指针；
&emsp;&emsp; **(2)** 传给delete的必须是 **指向动态分配的内存的指针** 或 **空指针**；
```cpp 
int i, *pi1 = &i, *pi2 = nullptr;
double *pd = new double(33), *pd2 = pd;
delete i; // 错误: i不是指针
delete pi1; // 未定义: pi1 指向的是一个局部变量
delete pd; // 正确
delete pd2; // 未定义: pd2 指向的内存已经被释放了
delete pi2; // 正确: delete 空指针是没问题的

const int *pci = new const int(1024);
delete pci; // 正确:  const 对象的值不能改变，但是可以被销毁。
```
&emsp;&emsp; **(3)** 释放动态分配的数组时，要在中间加`[]`，即应该使用`delete[] ptr`:
```cpp
string *const p = new string[n]; // 构造 n个 空string(第一次赋值：n个string对象都被默认初始化为空

// 相关操作，略...

delete[] p; // 释放
```
&emsp;&emsp; **(4)** 不能重复`delete`同一块内存

### 1.11 什么是 空悬指针(dangling pointer)？如何解决？
&emsp;&emsp; 对于指针 p，它指向的是一块动态分配的内存，在这块内存将在被delete后被释放，此时指针p仍然指向了这段被delete的指针，此时指针p就被称为 空悬指针(dangling pointer)。
&emsp;&emsp; 内存delete之后，马上将指向该内存的指针置为 nullptr，但这也只能提供有限的保护，来看下面的代码：
```cpp
int *p(new int(42));
auto q = p;     // p 和 q 指向了相同的内存
delete p;       // p 现在是 空悬指针
p = nullptr;
```
此时虽然p被置为nullptr了，但q依然是一个空悬指针，在实际系统中，查找指向相同内存的所有指针是异常困难的，因此空悬指针很难被解决。
### 1.12 为什么说有一些动态内存除非在程序关闭时由OS回收外，永远不可能被释放？
我们来看下面的代码：
```cpp
// factory 返回了一个指向动态分配对象的指针
Foo* factory(T arg)
{
    // process arg as appropriate
    return new Foo(arg); // 调用者需要负责释放该动态内存
}

void use_factory(T arg)
{
    Foo *p = factory(arg);
    // 使用p但不delete它
}   // p在离开作用域后被释放，但是p指向的动态内存并没有被释放
```
在`use_factory`函数中，p离开了作用域，但是p指向的动态内存并没有被释放，而且由于 p 是唯一指向该动态内存的指针，因此在p被销毁后，它所指向的内存永远不可能被释放，除了在程序关闭时由OS回收。
### 3.13 使用 new 和 delete 管理动态内存有哪些常见的错误？
&emsp;&emsp;(1) 忘记delete，造成内存泄露
&emsp;&emsp;(2) 使用已经释放了的对象，可以通过将释放的内存置空来检测这种错误；
&emsp;&emsp;(3) 同一块内存被 释放多次。在有 两个指针 指向相同的动态内存时，这种错误很容易犯，因为第一次delete时，空间就已经还给了 堆，再delete的话可能会造成堆被破坏。



&emsp;
## 二、智能指针
### 2.1 标准库定义了哪些智能指针？它们分别用于什么场景？
&emsp; `shared_ptr` 和 `unique_ptr`：
&emsp;&emsp; `shared_ptr`允许多个指针指向同一个对象；
&emsp;&emsp; `unique_ptr`则独占所指向的对象。
### 2.2 智能指针是通过什么实现的？
&emsp;&emsp;和`vector`一样，智能指针也是模板实现的。
### 2.3 使用智能指针有什么好处？
&emsp;&emsp;智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针是一个类，当超出了类的实例对象的作用域时，会自动调用对象的析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。
&emsp;&emsp;也就是说，使用智能指针可以简化程序员的工作，避免 内存泄露
### 2.4 智能指针 可以 托管 非动态分配的对象吗？
&emsp;&emsp;默认情况下，不能，因为智能指针默认，只有指向动态分配的对象的指针才能交给 shared_ptr 对象托管。将指向普通局部变量、全局变量的指针交给 shared_ptr 托管，编译时不会有问题，但程序运行时会出错，因为不能析构一个并没有指向动态分配的内存空间的指针。
&emsp;&emsp; 如果想将智能指针绑定到 一个指向非动态分配的对象，需要提供自己的操作来替代delete



&emsp;
## 三、shared_ptr类
### 3.1 如何定义一个指向 string类型(值为：Hello world) 的 shared_ptr?
```cpp
shared_ptr<string>p1 = new string("Hello world"); // 错误
shared_ptr<string>p1(new string("Hello world"));  // 正确，用的是直接初始化
shared_ptr<string>p1 = make_shared<string>("Hello world");
```
### 3.2 定义一个 shared_ptr 时，若不对其进行显式初始化，变量的值是？
&emsp;&emsp;此时将执行默认初始化，里面是一个空指针。
### 3.3 shared_ptr支持的操作
#### 3.3.1 和unique_ptr共有的操作
| 操作               | 解释                                                                                     |
| ------------------ | ---------------------------------------------------------------------------------------- |
| `shared_ptr<T>sp`  | 空智能指针，可以指向类型为T的对象                                                        |
| `unique_ptr<T>up ` |
| `p	     `          | 将p当做 判断条件，若p指向了一个对象则返回`true`                                          |
| `*p	     `         | 解引用                                                                                   |
| `p->mem	 `         | 等价于(*p).mem                                                                           |
| `p.get()	 `        | 返回p中保存的指针，此时应该小心，因为若智能指针释放了其对象，则p指向的对象也同样被释放了 |
| `swap(p,q)`        | 交换p和q中的指针                                                                         |
| `p.swap(q)`        | 交换p和q中的指针                                                                         |
#### 3.3.2 shared_ptr独有的操作
| 操作                    | 解释                                                                                                                                     |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `make_shared<t>(args)`  | 返回一个shared_ptr，指向一个动态分配的类型为T的对象，并使用args初始化该对象                                                              |
| `shared_ptr<T>p(q)	 `   | p是shared_ptr q的拷贝，此操作增加q中的计数器，q中的指针必须能转换为T*                                                                    |
| `p=q	                 ` | p和q都是shared_ptr，所保存的指针必须能相互转换。此操作会递减p中的引用计数，递增q中的引用计数。若p中的引用计数变为0，则将其管理的内存释放 |
| `p.use_count()	 `       | 返回与p共享对象的智能指针数量，此操作可能很慢，平时主要用于调试。                                                                        |
| `p.unique()	     `      | 若p.use_count()为1，返回true;否则返回false                                                                                               |
#### 3.3.3 如何判断 指向string的智能指针p指向的值是否为空 比较安全？
和常规指针一样，取值之前先判空：
```cpp
if(p && p->empty)
    *p = "Hello";
```
### 3.4 make_shared 函数
#### 3.4.1 作用是？
&emsp;&emsp; make_shared 函数用于分配**动态内存**，可以说是最安全的分配和使用动态内存的方法，此函数 在动态内存中分配一个对象 并初始化它，然后返回指向此对象的 shared_ptr
#### 3.4.2 怎么用？
&emsp;&emsp; 
```cpp
shared_ptr<int> p1 = make_shared<int>(42);
shared_ptr<string> p2 = make_shared<string>(10, "9");
shared_ptr<int> p3 = make_shared<int>();    // 请求值初始化，p3指向的int的值为0
```
也可以和`auto`搭配使用：
```cpp
auto p4 = make_shared<vecrot<string>>();
```
#### 3.4.3 原理是？
&emsp; 和顺序容器的`emplace`操作一样，`make_shared`会调用的是其参数来构造给定类型的对象，比如：
&emsp;&emsp;  ① 对于 `make_shared<string>`,传递的参数必须和 string类 的某个构造函数相匹配；
&emsp;&emsp;  ② 对于 `make_shared<int>`,传递的参数必须可以能用来初始化一个int
### 3.5 shared_ptr 的 引用计数 规则是怎样的？
&emsp; 指向相同资源的所有 shared_ptr 共享“引用计数管理区域”，并采用原子操作保证该区域中的引用计数值被互斥地访问。因此每当进行 **拷贝** 或 **赋值** 操作时，每个`shared_ptr` 都会记录有多少个 其它的`shared_ptr` 和它一样指向了相同的对象，我们可以认为每个 `shared_ptr` 都有一个 关联计数器（通常被称为引用计数）：
&emsp;&emsp;(1) 每当我们拷贝一个 `shared_ptr`，该计数器都会递增，也就是说：① 将`shared_ptr`作为函数的参数传递给一个函数（拷贝）、 ② 将`shared_ptr`作为函数的返回值（拷贝）、③ 将`shared_ptr`拷贝给其它`shared_ptr`都会导致它关联的引用计数递增
&emsp;&emsp;(2) 每当我们给`shared_ptr`赋新值、或`shared_ptr`被销毁（比如一个局部`shared_ptr`离开其作用域）会导致关联的引用计数递减
#### 3.5.1 将一个 `shared_ptr`指针q 赋给 另一个`shared_ptr`指针r 会发生什么？
q的引用计数+1，r原来指向的对象 的引用计数-1
```cpp
auto r = make_shared<int>(99);
// 1. q的引用计数+1
// 2. r原来指向的对象 的引用计数-1
// 3. 因为r原来指向的对象只有一个引用者，所以r原来指向的对象将被自动释放。
r = q; 
```
### 3.6 shared_ptr 通过什么控制 管理的对象的 初始化 和 销毁？
初始化：元素的构造函数
销毁：shared_ptr类的 析构函数负责销毁对象，它会递减它所指向的对象的引用计数，如果引用计数变为0，则shared_ptr的析构函数 不但会销毁shared_ptr对象，还会释放 shared_ptr对象 所指向的内存：
```cpp
shared_ptr<Foo> factory(T arg)
{
    return make_shared<Foo>(arg);//返回shared_ptr，指向一个类型为Foo的对象，用类型为T的arg初始化
}

void use_factory_1(T arg)
{
    shared_ptr<Foo> p = factory(arg);// 调用factory初始化p，p所指对象的引用计数+1变成1
    // use_factory_1 执行结束，销毁p，p所指对象的引用计数-1变成0，p指向的内存也被释放
}

shared_ptr<Foo> use_factory_2(T arg)
{
    shared_ptr<Foo> p = factory(arg);// 调用factory初始化p，p所指对象的引用计数+1变成1
    return p;//返回p，p所指对象的引用计数+1变成2
}// use_factory_2 执行结束，销毁p，p所指对象的引用计数-1变成1，p指向的内存并不会被释放
```
注意比较 `use_factory_1` 和 `use_factory_2`：
&emsp; 因为`use_factory_1`中，`shared_ptr`对象p所指向内存  的引用计数为1，因此在函数结束后，`shared_ptr`对象p 被销毁，它指向的内存的引用计数编程了0，因此`shared_ptr`对象p指向的内存也被释放了。
&emsp;而在`use_factory_2`中，它返回了构造的`shared_ptr`对象，因此引用计数为2，即使函数结束后 `shared_ptr`对象p被销毁，它指向的内存也不会被释放。
### 3.7 使用 `shared_ptr` 就一定不会内存泄露了吗？
&emsp;&emsp;不是的，因为在最后一个指向它的`shared_ptr`被销毁前内存都不会释放，所以还是有可能会造成内存泄露的。
### 3.8 程序一般出于什么原因一定要使用容易造成 内存泄露的 动态内存？
(1) 程序不知道自己需要使用多少对象；
(2) 程序不知道所需对象的准确类型；
(3) 程序需要在多个对象间共享数据
### 3.9 定义和改变shared_ptr的其它方法
```cpp
shared_ptr<T>p(q)       // p管理内置指针q指向的对象，其中q必须指向new分配的内存，而且必须可以转换为T*类型
shared_ptr<T>p(u)       // 从 unique_ptr指针u 那里接管对象的所有权，然后将u置空
shared_ptr<T>p(q, d)    // p接管了 内置指针q 所指向的对象，q必须能转换为T*，并用d来替换delete释放资源

shared_ptr<T>p(p2)      // p是 shared_ptr对象p2的拷贝
shared_ptr<T>p(p2, d)   // 和上面一样，p是 shared_ptr对象p2的拷贝，但是会使用d来替换delete释放资源
```
### 3.10 使用new出来的指针初始化 shared_ptr时要注意什么？为什么？
&emsp;&emsp; **使用new初始化shared_ptr时**必须使用直接初始化，因为 `shared_ptr`类 中接收 指针参数 的构造函数是 `explicit` 的，因此我们不能使用 内置指针(new出来的指针) 隐式转换为一个智能指针：
```cpp
shared_ptr<int> p1 = new int(1024);     // 错误：不能用拷贝初始化，必须使用 直接初始化
shared_ptr<int> p2(new int(1024));      // 正确: 直接初始化
```
这个错误在返回`shared_ptr`指针时很容易犯：
```cpp
shared_ptr<int> clone(int p){
    return new int(p); // 错误：映射转换为 shared_ptr
}
```
应该这么写才对：
```cpp
shared_ptr<int> clone(int p){
    return shared_ptr<int>(new int(p)); // 正确：显示的创建了一个 shared_ptr
}
```
**注意：** 并不是`shared_ptr`不能进行 拷贝初始化，而是不能通过 内置指针 对 `shared_ptr` 进行拷贝初始化！
### 3.10.1 具体分析
```cpp
shared_ptr<int> p1 = new int(1024); 
```
上面的代码为什么行不通呢，因为`p1`的初始化隐式的要求编译器用 一个new返回的`int*`来创建一个shared_ptr，而智能指针中接受 指针参数 的构造函数是`explicit`的，因此我们不能使用 内置指针 隐式转换为一个智能指针，因此我们直接初始化：
```cpp
shared_ptr<int> p2(new int(1024));      // 正确: 直接初始化
```

### 3.11 混用 普通指针 和 智能指针有何风险？
来看下面这个函数：
```cpp
// ptr 在函数被调用时 被创建并初始化
void process(shared_ptr<int> ptr){
    // 使用 ptr
} // ptr 离开作用域后被销毁
```
然后考虑下面的调用：
```cpp
int *x(new int(1024)); //  x 是一个 指向动态内存的内置指针
process(x); // 错误1: 无法将 int* 转换为 shared_ptr<int>
process(shared_ptr<int>(x)); // 正确, 用内置指针来初始化 实参
int j = *x; // 错误2：未定义的行为: x 在调用完process后就被释放了
```
上面的代码显然是错误的：
&emsp;&emsp;**错误1**：使用new出来的指针初始化shared_ptr时只能直接初始化，而直接传 x 进去，是用x对 process的形参ptr 进行拷贝初始化，这样显然是错误的；
&emsp;&emsp;**错误2**：因为我们将 一个临时的shared_ptr(即`shared_ptr<int>(x)`) 传给了 process函数，内置指针x 将对 rocess的形参ptr 进行直接初始化，这是合法的，但在process函数结束后，实参ptr就被销毁了，因为ptr和x指向的是同一动态内存，因此后面对 指针x访问显然是错误的。
正确的调用应该为：
```cpp
shared_ptr<int> p(new int(42)); // 此时只能指针p的引用计数为1
process(p); // 1. 通过p对形参ptr进行拷贝初始化，因此在函数里面时，p的引用计数为 2
            // 2. 函数调用结束后，参数ptr被销毁，此时p的引用计数为1

int i = *p; // 正确: p的引用计数为1
```
**总结：**使用一个内置指针来访问一个智能指针所负责的对象是很危险的，因为我们无法知道对象何时被销毁。
### 3.12 为什么推荐make_shared函数，而不是new运算符？
&emsp;&emsp; 智能指针`shared_ptr`可以协调对象的析构，但这仅限于其自身的拷贝(指的是`shared_ptr`对象之间的拷贝)，使用 `make_shared`函数可以在 分配对象的同时 就将 `shared_ptr`与其绑定，从而避免了无意间将同一块内存绑定到了多个独立的`shared_ptr`上
### 3.13 `shared_ptr`的 get函数
#### 3.13.1 get函数的作用？
&emsp;&emsp;智能指针定义了一个`get`函数，它返回一个内置指针，指向智能指针管理的对象。
#### 3.13.2 为什么要定义 get函数？
&emsp;&emsp;这个函数时为了这样一种情况而设计的：我们需要向不能使用智能指针的代码传递一个内置指针
#### 3.13.3 使用建议
&emsp;&emsp; 不要用get初始化另一个智能指针或为智能指针赋值，我们来看下面的代码：
```cpp
shared_ptr<int> p(new int(42)); // 智能指针p 的引用计数为 1
int *q = p.get();               // 正确: 但要保证 q不会释放它指向的内存
{   // 新的程序块
    // 未定义: 两个独立的 shared_ptrs 指向了相同的内存
    shared_ptr<int>r(q);
} // 程序块结束, r被销毁，且它指向的内存也被delete
int foo = *p; // undefined; the memory to which p points was f
```
在上面的代码中，当我们使用p时会发生未定义的行为，而且当p销毁时，这块内存会被第二次delete
### 3.14 `shared_ptr`的 reset函数
reset()函数有如下几个版本：
```cpp
p.reset()       // 若p是唯一指向其对象的 shared_ptr，则reset会释放该对象
p.reset(q)      // 令 p 指向 内置指针q
p.reset(q,d)    // 令 p 指向 内置指针q，使用d来释放q，而不是delete
```
**注意事项：**
不能用get出来的指针reset另一个智能指针，道理和前面一样。
## 3.15 动态内存 和 异常
### 3.15.1 为什么说异常 时很可能导致内存泄露？
```cpp
void f()
{
    int *ip = new int(42); // dynamically allocate a new object
    // 此处发生异常，而且未被捕获
    delete ip; // 
}
```
在上面的代码中，异常发生在 new 和 delete 之间，这就导致指针ip指向的动态内存 永远不可能被释放。
### 3.15.2 如何保证 异常发生的时候 资源能被正常释放？
#### (1) 捕获异常，在异常处理模块里将资源释放
```cpp
void f()
{
    try{
        int *ip = new int(42); // dynamically allocate a new object
        // 此处发生异常，而且未被捕获
        delete ip;
    }
    catch(erro){
        delete ip; // 
    }
}
```
#### (2) 使用智能指针
```cpp
void f()
{
    shared_ptr<int> sp(new int(42)); // allocate a new object
    // 此处发生异常，而且未被捕获
}   // 因为sp是智能指针，sp指向的动态内存将被自动释放
```
### 3.16 定义自己的释放操作
#### 一般什么情况下需要定义自己的释放操作？
&emsp;&emsp; 默认情况下，`shared_ptr`假定它正指向的是动态内存，当一个`shared_ptr`被销毁时，它默认的调用delete来对其进行释放。
&emsp;&emsp; 因此如果你想用 `shared_ptr` 来帮你管理非动态内存，那你就要自己提供一个操作 来替代 默认的delete行为。
&emsp;&emsp; 一个定义良好的C++类都应该定义了析构函数来负责清理对象使用的资源，但是有一些为 C和C++ 定义的类就不一定有析构函数了，比如 网络库，这个时候如果你想用`shared_ptr` 来帮你管理对象的话，那就应该 定义自己的操作 来替代delete释放资源。
#### 3.16.2 下面的代码有什么问题？应该怎么解决？
下面是C和C++都会使用的网络库，其中 `connection`类 没有析构函数
```cpp
struct destination;     // 表示正在连接什么
struct connection;      // 使用连接所需的信息
connection connect(destination*);   // 打开连接
void disconnect(connection);        // 关闭连接
void f(destination &d /* other parameters */)
{
    connection c = connect(&d);// 获取了一个连接
    // 使用该连接
}
```
**问题：**
&emsp;&emsp; 因为 `connection`类 没有析构函数，而上面的函数`f()`并没有关闭该连接，而且在函数结束时没有关闭该连接，这就导致资源没有被释放。
**解决：**
可以自己定义释放操作，然后使用`shared_ptr`管理：
```cpp
void end_connection(connection *p)
{
    disconnect(*p);
}
```
然后把 函数`f()` 改成：
```cpp
void f(destination &d /* other parameters */)
{
    connection c = connect(&d);// 获取了一个连接
    shared_ptr<connection>p(&c, end_connection);
    // 使用该连接
}
```
这样即使 函数`f()` 不关闭连接，connection也会被正常关闭，当函数结束是，将自动调用 `end_connection`，接下来，`end_connection`会调用disconnect，从而确保连接被关闭。
### 3.17 智能指针陷阱
(1) 不使用相同的内置指针 初始化(或reset) 多个 智能指针
(2) 不 delete get() 返回的指针
(3) 不使用 get() 初始化或reset 另一个只能指针
(4) 如果你使用 get() 返回的指针，记住当最后一个对应的智能指针被销毁后，你的指针就变为无效了
(5) 如果你使用只能指针管理的资源不是new分配的内存，记住传递一个删除器给它。



&emsp;
## 四、 unique_ptr
### 4.1 `unique_ptr` 和 `shared_ptr` 有何区别？
&emsp;&emsp; 和`shared_ptr`不一样的是，在同一时刻只能允许有一个 `unique_ptr` 指向一个给定的对象。
&emsp;&emsp; 另一个区别就是：两种指针的删除器的差异。对于shared_ptr来说，删除器是可以重载的，所以其类型是在运行时绑定。而unique_ptr的删除器不能重载，且是unique_ptr类的一部分，在其编译时绑定（具体介绍见第16章的笔记）。
### 4.2 有无 `std::make_unique`？
&emsp;&emsp; 在C++11是没有的，`std::make_unique`是在C++14里加入标准库的
### 4.3 如何新建 `unique_ptr`？
鉴于在C++11没有`std::make_unique`函数，我们只能通过将其绑定到一个new返回的指针上，**类似于`shared_ptr`，初始化`unique_ptr`时也必须采用直接初始化的方式：**
```cpp
unique_ptr<double>p1; // 一个可以指向double的 unique_ptr
unique_ptr<int>p2(new int(42));
```
### 4.4 unique_ptr的操作

| 操作                     | 解释                                                          |
| ------------------------ | ------------------------------------------------------------- |
| `unique_ptr<T> u1      ` | 空unique_ptr，将使用delete释放资源                            |
| `unique_ptr<T, D> u2   ` | 空unique_ptr，将使用 类型为D的可调用对象 来替代delete释放资源 |
| `unique_ptr<T, D> u2(d)` | 空unique_ptr，将使用 类型为D的对象d 来替代delete释放资源      |
| `u=nullptr             ` | 释放u指向的对象，并将u置空                                    |
| `u.release()           ` | u释放对指针的控制权，返回其保存的指针，然后将u置空            |
| `u.reset()             ` | 释放u指向的对象，并将u置空                                    |
| `u.reset(q)            ` | 释放u指向的对象，并将u指向q                                   |
| `u.reset(nullptr)      ` | 释放u指向的对象，并将u置空                                    |

### 4.5 如何用 一个 unique_ptr 给 另一个unique_ptr赋值？
&emsp;&emsp; 因为 `unique_ptr` 独占 其指向的对象，因此 `unique_ptr` 并不支持普通的拷贝或赋值：
```cpp
unique_ptr<string> p1(new string("Stegosaurus"));
unique_ptr<string> p2(p1); // 错误:  unique_ptr 不支持拷贝，以这种方式拷贝是 shared_ptr的独有操作
unique_ptr<string> p3;
p3 = p2; // error:  unique_ptr不能赋值
```
虽然不能拷贝或赋值 `unique_ptr` ，但可以通过调用`realease` 或 `reset` 将指针的所有权从一个（非const） `unique_ptr` 转义给另一个 `unique_ptr` ：
```cpp
unique_ptr<string> p1(new string("Stegosaurus"));

//这将导致指针的所有权从p1转到p2，执行过程如下
//    1. p1.release()将使p1放弃对指针的控制权，并将其置空，然后返回 指针
//    2. p1.release()返回的指针 对p2进行了直接初始化，
unique_ptr<string>p2(p1.release());


unique_ptr<string>p3(new string("Trex"));

// p3的所有权将转到p2：
//   1. p3.release()使p3放弃对指针的控制权，并将其置空，然后返回 指针
//   2. p2.reset(返回的指针) 导致 p2指向的资源被释放，然后将p2指向第一步返回的指针
p2.reset(p3.release());
```
### 4.6 既然 `unique_ptr` 不能被复制和拷贝，我们如何返回一个 `unique_ptr`对象呢？
&emsp;&emsp; 不能拷贝 `unique_ptr`对象 有一个例外：我们可以拷贝或赋值一个将要被销毁的`unique_ptr`，比如：
(1) 直接从函数中返回一个`unique_ptr`：
```cpp
unique_ptr<int> clone(int p){
    return unique_ptr<int>(new int(p));
}
```
(2) 返回一个局部对象的拷贝：
```cpp
unique_ptr<int> clone(int p){
    unique_ptr<int> ret (new int(p));
    return ret;
}
```
上面的两段代码都是正确的，因为编译器知道 返回的对象 即将被销毁，因此编译器将执行一种特殊的 "拷贝"，这将在13.6.2小节中介绍。
### 4.7 如何给 `unique_ptr` 自定义 删除器？
&emsp;&emsp; `unique_ptr`管理删除器的方式和`shared_ptr`有些不一样（原因见16.1.6节），
&emsp;&emsp; 重载一个`unique_ptr`的删除器会影响到`unique_ptr`类型以及如何构造(或reset)该类型的对象，我们必须在尖括号中 `unique_ptr`指向类型之后 提供删除器的类型：
```cpp
unique_ptr<objT, delT> p (new objT, fcn);
```
下面我们用`unique_ptr`来重写前面的连接程序：
```cpp
void f(destination &d /*其它参数*/)
{
    connection c = connect(&d);
    // decltype 返回函数类型，加上 * 之后就是函数指针了
    unique_ptr<connection, decltype(end_connection)*> p(&c, end_connection);
    // 使用连接
    // 函数f 结束，connection将被正确关闭
}
```



&emsp;
## 五、 weak_ptr
### 5.1 weak_ptr 有何特点？
&emsp;&emsp; `weak_ptr`是弱智能指针对象，它不控制所指向对象生存期的智能指针，它指向由一个`shared_ptr`管理的智能指针。将一个`weak_ptr`绑定到一个`shared_ptr`对象，不会改变`shared_ptr`的引用计数。一旦最后一个所指向对象的`shared_ptr`被销毁，所指向的对象就会被释放，即使此时有`weak_ptr`指向该对象，所指向的对象依然被释放。
### 5.2 为什么要引入 weak_ptr ？
&emsp;&emsp; 智能指针一个很重要的概念是“所有权”，所有权意味着当这个智能指针被销毁的时候，它指向的内存（或其它资源）也要一并销毁。这技术可以利用智能指针的生命周期，来自动地处理程序员自己分配的内存，避免显示地调用delete，是自动资源管理的一种重要实现方式。
&emsp;&emsp; 为什么要引入“弱引用”指针呢？弱引用指针就是没有“所有权”的指针。有时候我只是想找个指向这块内存的指针，但我不想把这块内存的生命周期与这个指针关联。这种情况下，弱引用指针就代表“我指向这东西，但这东西什么时候释放不关我事儿……”
&emsp;&emsp;  有些地方为了方便，直接用原始指针（raw pointer）来表示弱引用。然后用这种原始指针，其弱引用的含义不够明确，万一原始指针指向的动态内存被释放了，你再实用原始指针访问这块内存就危险了，而`weak_ptr`的访问是通过`lock()`此函数将检查`weak_ptr`指向的对象是否存在，如果存在则返回返回一个指向w的对象的`shared_ptr`，否则返回一个空指针，这就避免了访问被释放过的内存的可能。
### 5.3 weak_ptr 的操作
| Column A            | Column B                                                                       |
| ------------------- | ------------------------------------------------------------------------------ |
| `weak_ptr<T> w    ` | 空weak_ptr                                                                     |
| `weak_ptr<T> w(sp)` | 与 shared_ptr指针sp 指向相同对象 的 weak_ptr                                   |
| `w = p            ` | p 可以是 shared_ptr 或 weak_ptr，赋值后w与p共享对象                            |
| `w.reset()	      `  | 将当前 weak_ptr 指针置为空指针。                                               |
| `w.use_count()	  `  | 查看指向和当前 weak_ptr 指针相同的 shared_ptr 指针的数量。                     |
| `w.expired()	  `    | 如果`w.use_count()`为0，返回true，否则返回false                                |
| `w.lock()	      `   | 如果`w.expired()`为true，返回一个空指针，否则返回一个指向w的对象的`shared_ptr` |
### 5.4 如何通过 weak_ptr 访问对象？
&emsp;&emsp; 由于对象可能不存在，因此我们不能使用`weak_ptr`直接访问对象，而必须使用`lock()`，此函数检查`weak_ptr`指向的对象是否存在，如果存在则返回返回一个指向w的对象的`shared_ptr`，否则返回一个空指针：
```cpp
if(shared<int> np == wp.lock()){ // 若 np不为空则条件成立
    // do something here.
}
```



&emsp;
## 六、 动态数组
### 6.1 为什么需要动态定义数组？
&emsp;&emsp; 很多情况下，数组的长度必须在程序运行时动态的给出，但c++要求定义数组时，**必须在编译时就能确定数组的大小**，要不然编译通不过，此时可以通过new定义动态数组：
```cpp
int n = 10;
int a[n];   // 错误，数组的空间在编译时就要确定，但n的值运行时才能确定，因此编译器并不知道 i 的值是多少，所以报错
```
注意：在g++编译器中上述代码并没有报错，但这并不意味着就合法，在其它 非g++ 编译器中会报错。
### 6.2 为什么说 动态数组 不是 数组？
&emsp;&emsp; 当使用new分配一个数组时，我们并未得到一个数组类型的对象，而是得到一个数组元素的指针。
&emsp;&emsp; 由于分配的内存并不是一个数组类型，因此不能对动态数组调用`begin()`或`end()`这些函数。这些函数使用数组维度来返回指向首元素和尾后元素的指针。同理，也不能用范围for语句来处理（所谓的）动态数组中的元素。
&emsp;&emsp;**要记住我们说的动态数组并不是数组类型，这是很重要的。**
### 6.3 动态数组 的 初始化
&emsp;&emsp; **默认情况下，new分配的对象，不管是单个分配还是数组中的，都是默认初始化的。**
&emsp;&emsp; 也可以对其进行 值初始化，方法和前面一样，在大小后面跟上一对 空的圆括号：
```cpp
int *pia = new int[10];             // block of ten uninitialized ints
int *pia2 = new int[10]();          // block of ten ints value initialized to 0
string *psa = new string[10];       // block of ten empty strings
string *psa2 = new string[10]();    // block of ten empty strings
```
C++11中，还能使用一个元素初始化器的花括号列表：
```cpp
int *pia = new int[10]{0,1,2,3,4,5,6,7,8,9};
```
**如果初始化器的数目小于元素数目：**
&emsp;&emsp; 有初始化器的将使用给定的初始化器初始化，剩余的进行 值初始化：
```cpp
//前四个用给定的初始化器初始化，剩余的进行 值初始化。
string *psa3 = new string[10]]{"a", "an", "the",string(3,'x')};
```
**如果初始化器的数目大于元素数目：**
&emsp;&emsp; 会抛异常：`bad_array_new_length`
### 6.4 创建动态数组的时候，如果 大小指定为0 会发生什么？
&emsp;&emsp; 虽然我们不能创建一个大小为0的静态数组，但创建动态数组时，大小指定为0是合法的，不过不能对大小为0的动态数组解引用，毕竟它不指向任何元素：
```cpp
char arr[0];            // 错误：静态数组大小不能为0
char *cp = new char[0]; // 正确：但不能对cp解引用
```
### 6.5 释放动态数组
#### &emsp;6.5.1 如何释放？
&emsp;&emsp; 和其它动态内存一样，我们也是用`delete`来释放动态数组，但是一种特殊形式的`delete`：
```cpp
delete p;       // p  必须是指向一个动态分配的对象 或 为空；
delete [] pa;   // pa 必须指向一个动态分配的数组或为空
```
#### &emsp;6.5.2 动态数组的释放顺序是怎样的？
&emsp;&emsp; 对于动态数组，释放时是逆序的，即最后一个元素首先被释放，然后往前推，直到首元素被销毁。
#### &emsp;6.5.3 释放 动态数组 时，如果忘了加 `[]`会发生什么？
&emsp;&emsp; 行为是未定义的，而且编译器很可能不会给出任何警告，这样我们的程序可能在执行过程中没有任何警告的情况下行为异常。
### 6.6 可以 智能指针 管理 动态数组吗？
可以的
#### &emsp;6.6.1 使用 `unique_ptr`来管理
&emsp; 标准库提供了 一个可以管理new分配的数组的`unique_ptr`版本。
&emsp; **(1) 如何使用？**
&emsp; 为了使用unique_ptr管理动态数组，我们必须在对象类型后面跟一对空方括号：
```cpp
unique_ptr<int[]>up(new int[10]); // up 指向 10个未初始化的int数组
up.release());  // 将自动调用 delete[] 销毁
```
上面的代码中，类型说明符中的方括号`(<int[]>`)指出up是一个数组，而不是一个int，由于up指向的是数组，因此当up销毁它管理的对象时，会使用 `delete[]`而不是`delete`。
&emsp;**(2) 标准库为 指向数组的unique_ptr 提供了哪些操作？**
&emsp;标准库为指向数组的`unique_ptr`提供的操作与其它的有些不一样：
&emsp;&emsp; ① 不能使用点(`.`)、箭头成员运算符(`->`)，毕竟`unique_ptr`指向的是数组而不是单个对象；
&emsp;&emsp; ② 可以像使用静态数组一样，通过下表来访问数组中的元素；
| 操作                   | 作用                                           |
| ---------------------- | ---------------------------------------------- |
| `unique_ptr<T[]> u   ` | u可以指向一个动态分配的数组，数组的元素类型为T |
| `unique_ptr<T[]> u(p)` | u指向 内置指针p所指向动态数组                  |
| `u[i]                ` | 返回 u所指向的 动态数组中位置i处的对象         |
下面是一个使用实例：
```cpp
unique_ptr<int[]>up(new int[10]);
for(int i = 0; i < 10; ++i)
    up[i] = i;
```
#### &emsp;6.6.2 使用 `shared_ptr`来管理
&emsp;&emsp; 和`unique_ptr`不一样的是，标准库 **没有** 为`shared_ptr`提供管理动态数组的功能，因此若想使用`shared_ptr`来管理 动态数组，我们得自己定义 删除器：
```cpp
shared_ptr<int> sp(new int[10], [](int *p) { delete [] p; });
```
关于上面的lambda表达式：
>① `[]`
&emsp;&emsp; 此lambda表达式不需要捕获任何局部变量，而lambda表达式的捕获列表是不能为空的，因此为空；
② `(int *p)`
&emsp;&emsp; 此lambda表达式的作用是 删除动态数组，因此需要传入待删除的动态数组(类型为`(int *p)`)，因此形参为`(int *p)`；
③ `{delete [] p;}`
&emsp;&emsp; 释放动态数组 p

另外，`shared_ptr` 不支持动态数组管理这一特性 也会影响我们访问数组中的元素：不能使用下标访问，只能通过`get()`获取内置指针然后递增：
```cpp
shared_ptr<int> sp(new int[10], [](int *p) { delete [] p; });
for(int i = 0; i < 10; ++i)
    *(sp.get() + i) = i;
```
#### &emsp;6.6.3 用`shared_ptr`管理动态数组时，若没有为提供删除起会发生什么？
&emsp;&emsp; 默认情况下，`shared_ptr`会调用`delete`来释放内存，这和释放动态内存忘了加`[]`的问题是一样：未定义
#### &emsp;6.6.4 使用智能指针管理动态数组的总结
&emsp;&emsp; 鉴于 标准库没有为 `shared_ptr` 提供管理 动态内存的操作，各种操作都需要自己来定义，因此使用`unique_ptr`将会是更好的选择。



&emsp;
## 七、 allocator类
### 7.1 为什么需要allocator类
&emsp;&emsp; 我们**在分配单个对象时**，往往希望将内存的分配和对象的初始化组合在一起，这样我们几乎肯定可以知道对象应该有什么值。
&emsp;&emsp; 但我们**在分配一大块内存时**，我们通常会希望在这块内存上按需构造对象，也就是说希望将 内存分配 和 对象的构造 分离，这意味着我们可以分配大块内存，但只有在真正需要的时候才执行对象创建构造。
我们来看下面的代码：
```cpp
string *const p = new string[n]; // 构造 n个 空string(第一次赋值：n个string对象都被默认初始化为空string)
string s;
string *q = p; // q 指向第一个 string
while (cin >> s && q != p + n)
    *q++ = s; // 第二次赋值：从标准输入读取数据来赋予 *q 一个新值
const size_t size = q - p; // 记录读取的string总个数
// 此处使用数字
delete[] p; // 释放
```
在上面的代码中，有两个不完美的地方：
> ① 我们构造了 n个string，但我们可能不需要这么多个string；
> ② p指向的元素都被赋值了两次，第一次时用new构造时执行的默认初始化，第二次是while循环内的赋值

&emsp;&emsp; 如果我们可以做到将 内存分配 和 对象的构造 分离，那上面两个问题能得到解决，但我们都知道 new运算符 在分配大块内存时将 内存的分配 和 对象的构造 组合在了一起，因此我们需要一个方法可以实现 在分配一大块内存时将 内存分配 和 对象的构造 分离，而allocator类就可以做到这一点。
&emsp;&emsp; **还有很重要的一点**： 使用new动态分配数组时，需要依赖 默认构造函数，万一类没有定义默认构造函数的话，这就意味着这个类不能动态分配数组。
**总结：** alocator类可以做到 在分配一大块内存时将 内存分配 和 对象的构造 分离。
### 7.2 allocator类 是用什么实现的？
&emsp;&emsp;和vector一样，是通过模板实现的。
### 7.3 allocator类 定在哪个头文件？
&emsp;&emsp;定义在`memory`头文件中
### 7.4 allocator类 支持哪些操作？
&emsp;&emsp;
| 操作                   | 说明                                                                                                                                                                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `allocator<T> a	     ` | 定义一个名为a的allocator对象，它可以为类型为T的对象分配内存，然后返回一个指针                                                                                                                           |
| `a.allocate(n)	     `  | 分配一段原始的、未构造的内存，保存n个类型为T的对象                                                                                                                                                      |
| `a.deallocate(p, n)	 ` | 释放内存（从T*指针p开始的内存），这块内存保存了n个类型为T的对象；p必须是一个先前由allocate返回的指针，n必须是p创建时所要求的大小，在调用deallocate之前，用户必须对每个在这块内存中创建的对象调用destroy |
| `a.construct(p, args)` | 在p指向的内存中构造一个对象，p必须是一个类型为T*的指针，指向一块原始内存；args可以是零个或多个参数，用来初始化构造的对象                                                                                |
| `a.destroy(p)	     `   | 对p指向的对象执行析构函数，p为T*类型的指针                                                                                                                                                              |
### 7.5 如何使用 allocator类？
(1) 构造allocator对象
需要指明这个allocator可以分配的对象类型：
```cpp
allocator<string> alloc;
```
(2) 分配内存
分配内存时需要指定元素个数：
```cpp
const size_t n = 10; 
auto const p = alloc.allocate(n);
```
(3) 构造元素
&emsp;&emsp; 用 `allocate`分配的内存是未构造的，需要通过`construct()`成员函数来在分配的内存中构造对象。
&emsp;&emsp; `construct()`成员函数接收 一个指针 和 零个或多个额外参数，用力啊在给定位置构造一个元素，这些额外参数必须是与构造对象的类型相匹配的合法的初始化器。
(4) 销毁元素
&emsp;&emsp; 销毁p指向的对象，但是不会释放空间，也就意味着，这段空间依然可以使用。
(5) 释放内存
&emsp;&emsp; 传给`deallocate()`的指针不能为空，且必须指向前面由`allocate`分配的内存；并且传给`deallocate()`的大小必须参数必须和调用`allocate`请求分配内存时一样。
```cpp
alloc.deallocate(p, n); 
```
```cpp
int main ()
{
    // (1) 构造allocator对象
    allocator<string> alloc;    

    // (2) 分配内存
    const size_t n = 10; 
    auto const p = alloc.allocate(n);

    // (3) 构造元素
    auto q = p;
    alloc.construct(q++);
    alloc.construct(q++, 10, 'c');
    alloc.construct(q++, "hi");
    int size = q - p;  
    while(--size >= 0)
        cout << size << " : " << *(p + size) << endl;
    
    // (4) 销毁元素
    while(p != q) // 注意，这里是挨个销毁
        alloc.destroy(--q); 
    
    // destroy后的对象还能重用
    auto r = p;  
    alloc.construct(r, "we can reuse the destroyed memory");
    cout << "*r: " << *r << endl;

    // (5) 释放内存
    alloc.deallocate(p, n); 

    return 0;
}
```
输出：
>2 : hi
>1 : cccccccccc
>0 : 
>*r : we can reuse the destroyed memory

### 7.6 allocator类中 拷贝和填充 未初始化内存的算法
&emsp;&emsp;标准库为 allocator类 定义了两个伴随算法,定义在头文件memory中：
| Column A                     | Column B                                                |
| ---------------------------- | ------------------------------------------------------- |
| uninitialized_copy(b,e,p),   | 将迭代器b到e之间的元素 拷贝到 p指定的未构造的原始内存中 |
| uninitialized_copy_n(b,n,p), | 从迭代器b开始, 拷贝n个元素 到p指定的未构造的原始内存中  |
| uninitialized_fill(b,e,t),   | 在迭代器b到e之间创建对象, 对象均为 t的拷贝              |
| uninitialized_fill_n(b,n,t), | 从迭代器b开始创建n个对象,每个对象都是t的拷贝            |

```cpp
// 构造allocator对象
allocator<string> alloc;   

// 分配比 vi所占空间大一倍的动态内存
auto p = alloc.allocate(vi.size() * 2);

// 通过拷贝vi中的元素来构造从p开始的元素
// q的值为： p+vi.size()，即q指向 最后一个元素后面的位置。
auto q = uninitialized_copy(vi.begin(), vi.end(), p);

// 将剩余的元素初始化为42
uninitialized_fill_n(q, vi.size(), 42);
```
**算法的返回值：**
&emsp;&emsp; 类似于`copy()`算法，`uninitialized_copy()`返回 递增后的目的位置迭代器
**需要注意啊的是：**
&emsp;&emsp; 使用这些算法时，一定要保证空间够用！

### 7.7 `allocator::construct`的构造原理是什么？
&emsp;&emsp; 先说结论吧，`allocator::construct`可使用对用元素类型的任意构造函数，它会根据用户传给它的参数来匹配对应的构造函数。
我们写一个例子来验证一下：
```cpp
class Tracer{
public:
    Tracer(){
        cout<< "construct without parameter!" << endl;
    }
    Tracer(int v){
        cout << "construct wit a int : " << v << "!" << endl;
    }
    ~Tracer(){
        cout << "destroy!" << endl;
    }
};


int main()
{
    allocator<Tracer> alloc;
    auto const p = alloc.allocate(5);
    auto q = p;
    alloc.construct(q++);     // 构造第一个元素
    alloc.construct(q++, 2);  // 构造第二个元素
    while(q != p)
        alloc.destroy(--q);
    alloc.deallocate(p, 5);
}
```
**运行结果：**
```
construct without parameter!
construct wit a int : 2!
destroy!
destroy!
```
根据运行结果，我们可以看到`allocator::construct`其实是根据用户传给它的参数来匹配对用的构造函数的：
* `alloc.construct(q++)` : 调用的是 `Tracer()`
* `alloc.construct(q++, 2)` : 调用的是 `Tracer(int v)`






&emsp;
&emsp; 
## 8. `shared_ptr`、`unique_ptr`分别在哪个头文件？
&emsp;&emsp; 都在 `memory`头文件中。




&emsp;
## 文本查询程序
、TODO:  12.3小结

https://zhuanlan.zhihu.com/p/63488452
https://www.zhihu.com/question/61008381
https://www.zhihu.com/question/319277442