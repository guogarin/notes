
# 第十三章 拷贝控制

## 1. 拷贝控制操作
### 1.1 有哪些拷贝控制操作？
拷贝构造函数(copy constructor)
拷贝赋值运算符(copy assignment operator)
移动构造函数(move constructor)
移动赋值运算符(move assignment operator)
析构函数(destructor)
### 1.2 这些拷贝控制操作各自的作用分别是什么？
拷贝构造函数、移动构造函数定义了: 当用同类型的另一个对象 初始化本对象时 做什么。
拷贝赋值运算符、移动赋值运算符定义了: 将一个对象 赋予同类型的另一个对象时 做什么。
析构函数定义了:当此类型对象销毁时做什么。
### 1.3 为什么要自己定义 控制操作？
&emsp;&emsp; 如果我们不自己显示定义这些操作，那编译器会帮我们定义，但问题是编译器定义的版本的行为并不是我们想要的，依赖这些编译器定义的操作有可能会造成灾难。






&emsp;
&emsp;
## 2. 拷贝构造函数
### 2.1 拷贝构造函数 在什么时候被调用？
&emsp;&emsp; 当用同类型的一个对象 **初始化** 另一个对象时（注意是初始化哦）
### 2.2 什么样的函数是 拷贝构造函数？
**书上的定义：** 如果一个构造函数的第一个参数是自身类类型的引用，且任何额外的参数都有默认值，则此构造函数是 拷贝构造函数。
从上述定义可以总结出  拷贝构造函数 有如下几个特点：
> ① 首先要是构造函数（即函数名为类名）；
> ② 第一个参数必须是 自身类类型的引用（注意是引用哦）；
> ③ 如果有其它参数，则必须有默认值。

下面分别是什么构造函数？
```cpp
class Foo {
public:
    Foo();                          // 默认构造函数
    Foo(const Foo&);                // 拷贝构造破函数
    Foo(int size);                  // 普通的构造函数
    Foo(const Foo&, int size);      // 普通的构造函数
    Foo(const Foo&, int size = 0);  // 拷贝构造函数
    ~Foo();                         // 析构函数
    // ...
};
```

### 2.3 拷贝构造函数 可以是 explicit的吗？
&emsp;&emsp; 拷贝构造函数检查会被 隐式地使用，因此拷贝构造函数通常不应该声明为explicit

### 2.4 拷贝构造函数 的首元素必须是const的吗？
&emsp;&emsp; 不是必须，虽然我们可以定义一个 非const引用 的拷贝构造函数，但是基于 `对于那些不会改变的变量，都尽量声明为const`的原则，因此还是声明为const比较好。

### 2.5 如果没有自己定义 拷贝构造函数，会发生什么？
&emsp;&emsp; 编译器会为我们生成 **合成拷贝构造函数**

### 2.6 合成默认构造函数 和 合成拷贝构造函数 的生成规则有何不同？
&emsp;&emsp; **合成默认构造函数** 只有在 类里没有任何构造函数时（包括 移动构造函数），编译器才会帮忙生成；而**合成拷贝构造函数**就不一样了，只要没有自己定义 拷贝构造函数，即使你定义了其它 构造函数，编译器也会帮你生成 合成拷贝构造函数。

我们来看下面的代码：
```cpp
class Foo_1 {
public:
    Foo_1(int size);                  // 普通的构造函数
};

class Foo_2 {
public:
    
};
```
在上面的代码中，编译器只会为类`Foo_1`生成 合成拷贝构造函数，但是会为 `Foo_2`生成 合成默认构造函数 和 合成拷贝构造函数 

### 2.7 合成拷贝构造函数 有什么作用？
有两个作用：
> (1) 一般情况下，合成拷贝构造函数 会将其参数的成员逐个赋值到正在创建的对象中去；
> (2) 对于某些类来说，合成拷贝构造函数 是用来 **阻止我们拷贝该类类型的对象** 的。

### 2.8 合成拷贝构造函数 的拷贝规则是怎样的？
针对不同类型的对象，合成拷贝构造函数 的拷贝规则 有所不同：
> **内置类型：** 直接拷贝；
> **类对象：** 使用该类的拷贝构造函数来拷贝；
> **数组：** 虽然我们不能拷贝数组，但合成拷贝构造函数会逐个拷贝数组中的对象，数组元素若是内置类型则直接拷贝，若是类类型，则 使用该类的拷贝构造函数来拷贝。

### 2.9 拷贝初始化 和 直接初始化区别:
&emsp;&emsp; **直接初始化:** 实际上是 要求编译器 使用普通的函数匹配规则 来选择与我们的参数最匹配的**构造函**数;
&emsp;&emsp; **拷贝初始化:** 实际上是 要求编译器 将右侧的对象拷贝到 正在创建的对象中,如果需要还要进行类型转换(先调"构造函数"匹配建立左侧对象->调 "拷贝构造函数/移动拷贝构造函数" 将右侧对象拷贝);

### 2.10 拷贝初始化一般在什么时候发生？
拷贝初始化通常使用拷贝构造函数来完成。拷贝构造函数不仅在我们用等号`=`定义变量时调用，在下列情况下也会调用：
> (1) 根据另一个同类型的对象显式或隐式初始化一个对象；
> (2) 函数调用时，将一个对象作为实参 传递给一个非引用类型的形参；
> (3) 从一个返回类型为非引用类型的函数返回一个对象；
> (4) 用花括号列表初始化一个数组中的元素或一个聚合类中的成员；
> (5) 标准库容器初始化，或者调用insert或push成员时，容器会对其元素进行拷贝初始化（与之相对的是，emplace成员创建的元素都是直接初始化）。

### 2.11 拷贝初始化 通过什么来完成？
拷贝构造函数 或 移动构造函数：
> 通常是使用 **拷贝构造函数** 来完成；
> **但如果一个类有一个移动构造函数**，则拷贝初始化有时会使用移动构造函数来完成。

### 2.12 拷贝构造函数 首对象必须是引用？
&emsp;&emsp; 是的，必须是引用。
&emsp;&emsp; 如果拷贝构造函数中的参数不是一个引用，那么就相当于采用了传值的方式(pass-by-value)，而传值的方式会调用该类的拷贝构造函数，从而造成无穷递归地调用拷贝构造函数。因此拷贝构造函数的参数必须是一个引用。

### 2.13 拷贝构造函数不能传值， 但可以传指针吗？
&emsp;&emsp; 可以自己定义一个以指针为参数的构造函数，并且自己在相应的情况下可以使用，但程序不会在需要拷贝构造函数时自动调用它。

### 2.14 explicit构造函数 和 拷贝初始化
&emsp;&emsp; 因为explicit构造函数 不能
```cpp
vector<int> v1(10);     // ok: direct initialization
vector<int> v2 = 10;    // error: constructor that takes a size is explicit
void f(vector<int>);    // f's parameter is copy initialized
f(10);                  // error: can't use an explicit constructor to copy an argument
f(vector<int>(10));     // ok: directly construct a temporary vector from an int
```

### 2.15 编译器可以绕过拷贝构造函数
.TODO: 不是很明白书上想表达的意思，后面回来再看。

### 2.16 为下面的类提供 一个 拷贝构造函数
```cpp
class HasPtr {
public:
    // 所有参数都有默认值，因此是个默认构造函数
    HasPtr(const std::string &s=std::string()): i(0), ps(new std::string(s)) { }

    HasPtr(const HasPtr&p): i(p.i), ps(new std::string(p.ps)) { }
    HasPtr& operator=(const HasPtr&);
    ~HasPtr(){delete ps; }
private:
    int i;
    std::string * ps;
};
```
**解答：**
&emsp;&emsp; 值得注意的是 拷贝构造函数也是构造函数，因此需要在构造函数初始值列表中对类的成员进行初始化，如果在花括号`{}`中定义的话就是赋值了，而不是初始化：
```cpp
class HasPtr {
public:
    // 其它成员略...
    HasPtr(const HasPtr& p): i(p.i), ps(new std::string(p.ps)) { }
private:
    // 私有成员，同略...
};
```





&emsp;
&emsp;
## 3. 拷贝赋值运算符
### 3.1 拷贝赋值运算符 在什么时候被调用？
&emsp;&emsp; 将一个对象 赋予 同类型的另一个对象时（注意是赋值哦）

### 3.2 拷贝赋值运算符 和 拷贝构造函数 的作用有何不一样？
拷贝赋值运算符 管的是 用 `=` 赋值
拷贝构造函数 管的是 拷贝初始化
```cpp
Sales_data trans, accum;
tranc = accum;  // 此时调用的是 拷贝赋值运算符

Sales_data a;
Sales_data b = a;// 此时调用的是 拷贝构造函数
```

### 3.3 为什么需要 拷贝赋值运算符？
&emsp;&emsp; 拷贝构造函数只能在初始化进行状态的赋予，若需要在初始化后再进行状态整体的赋予需要用到赋值构造函数。

### 3.4 如果没有自己定义 拷贝赋值运算符，会发生什么？
&emsp;&emsp; 编译器会为我们合成一个。

### 3.5 如何使用 拷贝赋值运算符？
&emsp;&emsp; 就和正常的赋值一样使用就行。

### 3.6 有等号`=`的时候一定是拷贝赋值运算符吗？
&emsp;&emsp; 等号`=`不一定是拷贝赋值运算符，发生在初始化时可能是拷贝构造函数或移动构造函数。

### 3.7 如何为类定义 自己的 拷贝赋值运算符？
&emsp;&emsp; 重载赋值运算符即可。
&emsp;&emsp; 重载运算符本质上是函数，其名字由`operator`关键字后接要定义的运算符号，如：
```cpp
class Foo{
public:
    // 返回类型为自己的引用
    // `operator`关键字后接要定义的运算符号，这里为 =
    Foo& operator=(const Foo&);
}
```

### 3.8 定义 自己的 拷贝赋值运算符 时要注意什么？
&emsp;&emsp; 为了和内置类型的赋值保持一致，重载赋值运算符时 通常返回一个 指向其左侧运算对象的引用。(内置类型的赋值运算符为什么要返回 左侧运算对象的引用 的原因见第四章的笔记)

### 3.9 拷贝赋值运算符 和 标准库 容器
&emsp;&emsp; 标准库通常要求 保存在容器中的元素类型 要具有 赋值运算符，而且返回值是左侧运算对象的引用。

### 3.9 对于下面的类定义，编译器生成的 合成拷贝运算符执行的操作是？
```cpp
class Sales_data {
public:
    /*  由于我们已经定义了其它形式的构造函数，因此编译器不会为我们合成默认构造函数；
    因此使用 `=default` 来要求编译器生成默认构造函数  */
    Sales_data() = default; 

    Sales_data(const std::string &s, unsigned n, double p):
                bookNo(s), units_sold(n), revenue(p*n) { }
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(std::istream&);
    std::string isbn() const { return bookNo; }
    Sales_data &combine(const Sales_data&);
private:
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};
```
很明显，上面的类没有定义自己的 拷贝赋值运算符，因此编译器会为其合成一个，这个 合成拷贝赋值运算符执行的操作相当于下面这个函数：
```cpp
// equivalent to the synthesized copy-assignment operator
Sales_data&
Sales_data::operator=(const Sales_data &rhs)
{
    bookNo = rhs.bookNo;            // bookNo成员是string类型，因此调用的是 string::operator=
    units_sold = rhs.units_sold;    // 使用内置的int赋值
    revenue = rhs.revenue;          // 使用内置的double赋值
    return *this;       // 返回一个自身的引用
}
```
### 3.10 编写拷贝控制运算符时，需要注意什么？
① 要保证用 将一个 对象赋予它自身时，拷贝赋值运算符 依然可以正常工作；
② 大多数赋值运算符结合了 析构函数和拷贝构造函数的工作。
关于上面两点，详情可以查看 C++ primer第五版 13.2.1节，或本文 8.2.1 行为像值的类`HasPtr`






&emsp;
&emsp;
## 4. 析构函数
### 4.1 析构函数的作用是？
&emsp;&emsp; 析构函数 执行和构造函数相反的操作：释放对象资源，析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

### 4.2 析构函数 有何特点？
名字由波浪线接类名构成；没有返回值，也不接受参数：
```cpp
class Foo {
public:
    ~Foo(); // destructor
    // ...
};
```

### 4.3 一个类能由几个析构函数？
&emsp;&emsp; 只能有一个，因为析构函数不接受参数，所以它不能被重载，因此对于一个类来说，只会有一个析构函数。

### 4.4 在析构函数中，成员的析构在什么时候？顺序是怎样的？
&emsp;&emsp; 和构造函数相反，首先执行函数体，然后再销毁成员，而且成员按初始化的顺序的**逆序**销毁。

### 4.5 析构函数在什么时候被调用？
&emsp;**对析构函数可以先这么理解：“对象生命周期结束时自动调用的函数”**
&emsp;无论何时一个对象被**销毁**，就会自动调用其析构函数：
> &emsp;变量在离开其作用域时被销毁；
> &emsp;容器（无论是标准库容器还是数组）被销毁时，其元素被销毁；
> &emsp;对于动态分配的对象，当对 指向它的指针使用`delete`运算符时被销毁；
> &emsp;对于临时对象，当创建它的完整表达式结束时被销毁；

```cpp
{   // 新作用域
    // p 和 p2 指向动态分配的对象
    Sales_data *p = new Sales_data;         // p 是内置指针
    auto p2 = make_shared<Sales_data>();    // p2 是一个 shared_ptr
    Sales_data item(*p);        // 拷贝构造函数 将*p拷贝到item
    vector<Sales_data> vec; // 局部变量
    vec.push_back(*p2);     // 拷贝p2指向的对象到vec
    delete p;   // 对 p 指向的对象执行 析构函数
}   // 退出局部作用域, 对item、p2、vec调用析构函数
    // 若 p2 的引用计数变为 0, 则对象将被释放
    // 销毁 vec 会销毁它里面的元素
```
在上面的代码中：
&emsp;&emsp; 我们需要直接管理的只有指针`p`指向的对象，因为它是 动态分配的Sales_data对象，因此在退出局部作用域前必须通过`delete`释放其内存，要不然就会造成内存泄露，但 `delete p` 这个语句是怎么释放对象的呢？ 对于对于动态分配的对象，当对 指向它的指针使用`delete`运算符时，该对象的类型的析构函数将被调用，然后释放该对象。
继续来看下面的代码：
&emsp;&emsp; 其余的对象 `item、p2、vec`都会在离开局部作用域后自动调用其析构函数进行，意味着在这些对象上会分别执行 `Sales_data`、`shared_ptr`、`vector`的析构函数。

```cpp
#include <iostream>
 
using namespace std;
 
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();   // 这是构造函数声明
      ~Line();  // 这是析构函数声明
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
 
void Line::setLength( double len )
{
    length = len;
}
 
double Line::getLength( void )
{
    return length;
}
// 程序的主函数
int main( )
{
   Line line;
 
   // 设置长度
   line.setLength(6.0); 
   cout << "Length of line : " << line.getLength() <<endl;
 
   return 0;
}
```
结果：
> Object is being created
> Length of line : 6
> Object is being deleted

### 4.6 下面的代码的运行原理是？
```cpp
#include <string>
#include <iostream>

using namespace std;

class B
{
public:
    B(int a = 10, int b = 10, string s = "Hello World"): num(a), total(b), str(s) {}
    ~B(){
        cout << "I am the destructor of class B" << endl;
    }   
    
    int num;
    int total;
    string str; 
    
};

class D
{
public:
    ~D() = default;
    B m;
};


int main()
{
    D demo;
    demo.~D();

    cout << demo.m.num << endl << demo.m.total << endl << demo.m.str << endl;

    return 0;
}
```
结果：
> I am the destructor of class B
> 10
> 10
> Hello World
> I am the destructor of class B

#### &emsp; 4.6.1 为什么被析构后还能继续访问 类D 的成员 m？
调用demo.~D()之后所有内存都释放掉了，后续的：
        `cout << demo.m.num << endl << demo.m.total << endl << demo.m.str << endl;`
虽然还能打印出结果，但实际上已经属于越界访问，是有问题的代码，是未定义行为。

#### &emsp;4.6.2 为什么 B类 的析构函数被调用两次？
&emsp;&emsp; 第一次 是显示调用，即`demo.~D();`
&emsp;&emsp; 第二次 是编译器隐式调用，因为变量`demo`离开其作用域(main函数)后被销毁，编译器自动调用其析构函数。

### 4.7 可以显示调用析构函数吗？
&emsp;&emsp; 不建议这么做，因为当对象离开其作用域时，编译器会隐式调用其析构函数，如果前面自己显示调用了的话，那么就是调用了两个析构函数，如果申请了堆内存(动态内存)，那么就是 对其进行了两次`delete`，后果很严重。

### 4.8 成员在什么时候被销毁？
&emsp;&emsp; 在**析构函数体** 执行完毕后，成员被销毁。
&emsp;&emsp; 认识到 析构函数体 自身 并不直接销毁成员是非常重要的，成员是在析构函数体之后隐含的析构阶段中被销毁的，在整个对象销毁的过程中，析构函数体是作为成员销毁步骤之外的另一部分而进行的。
### 4.9 什么时候不能依赖编译器生成的 合成析构函数 ？
&emsp;&emsp; 因为合成析构函数 不会 释放动态内存，因此当类中分配了动态内存时 不能依赖合成析构函数，而要自己定义一个析构函数来 delete动态内存。






&emsp;
&emsp;
## 5. 三五法则
### 5.1 为什么叫三五法则？
&emsp;&emsp; 由于拷贝控制操作是由三个特殊的成员函数来完成的，所以我们称此为“C++三法则”。在较新的 C++11 标准中，为了支持移动语义，又增加了移动构造函数和移动赋值运算符，这样共有五个特殊的成员函数，所以又称为“C++五法则”。
&emsp;&emsp; 也就是说，“三法则”是针对较旧的 C++98 标准说的，“五法则”是针对较新的 C++11 标准说的。为了统一称呼，后来人们把它叫做“C++ 三/五法则”。
### 5.2 自定义类的这些操作时应该遵循什么样的原则？原因是什么？
**(1) 需要自定义析构函数的类 也需要自定义 拷贝构造函数和拷贝赋值运算符**
&emsp;&emsp; 我们都知道，合成析构函数不会delete一个指针元素成员，因此在类中分配了动态内存时需要自定义析构函数来释放动态内存，我们来看`HasPtr`类的定义，它分配了动态内存，且自定义了析构函数来delete动态内存：
```cpp
class HasPtr {
public:
    HasPtr(const std::string &s = std::string()):ps(new std::string(s), i(0)) { };
    ~HasPtr() {delete ps;} 
    // 其它类成员
private:
    std::string *ps;
    int i;
}
```
`HasPtr`类看起来是没有任何问题的：它分配了动态内存，且在析构函数中对其进行了`delete`，避免了内存泄露，接下来我们来使用一下这个类：
```cpp
HasPtr f(HasPtr hp)
{
    HasPtr ret = hp; // 拷贝指定的 HasPtr
    // 处理ret
    return ret;
}
```
上面的代码中涉及到了两个`HasPtr`局部变量：实参hp和变量ret，在函数`f()`结束后它们将通过`HasPtr`的析构函数被释放，但是`HasPtr`类没有自定义 拷贝构造函数和拷贝赋值运算符，而是依赖编译器的合成版本，而编译器的合成版本仅仅是简单的拷贝指针成员，这导致多个指针指向了相同的动态内存，这就导致在析构 实参hp和变量ret 时，`delete`的是相同的内存，这显然是一个错误。
**如何解决上面的问题：** 自定义拷贝构造函数和拷贝赋值运算符，不仅仅是拷贝 指向动态内存的指针，而是对里面的内容也进行拷贝，这样就不会造成一块内存被`delete`多次的现象。
**总结**：
&emsp;&emsp; 如果一个类需要自定义析构函数，几乎可以肯定的是它也需要自定义拷贝构造函数和拷贝赋值运算符。
**(2) 需要拷贝操作的类 也需要赋值操作，反之亦然**
&emsp; 考虑这样一个`number`类：它为每个对象分配一个 独有的、唯一的序号，那这个类就不能使用合成版本的 拷贝构造函数和拷贝赋值运算符：
&emsp;&emsp; **若使用合成本的 拷贝构造函数：**`number new_num_1 = num;`，合成拷贝运算符 将会简单的赋值`num`变量中的序号给`new_num_1`，这造成`num`和`new_num_1`中的序号是一样；
&emsp;&emsp;** 若使用合成本的 拷贝赋值运算符：**`number new_num_2(num);`，合成拷贝构造函数 将会简单的赋值`num`变量中的序号给`new_num_1`，这造成`num`和`new_num_1`中的序号是一样；
**总结：**合成版本的拷贝构造函数和拷贝赋值运算符 仅仅是简单的拷贝，这就会造成新构造(拷贝)的类会和 给定对象的序号是一样，这不满足类的设计要求，因此需要自己定义 自定义 拷贝构造函数和拷贝赋值运算符 来完成新对象的构造，以满足每个对象的序号 是唯一的。
### 5.3 需要 自定义 拷贝构造函数和拷贝赋值运算符 的类 是否一定需要 自定义 析构函数？
&emsp;&emsp; 需要自定义析构函数的类 也需要自定义 拷贝构造函数和拷贝赋值运算符，但反过来却不一定。






&emsp;
&emsp; 
## 6. 显示要求编译器生成合成版本的 拷贝控制成员
### 6.1 如何 显示要求编译器生成合成版本的 拷贝控制成员？
&emsp;&emsp; 使用`=default`：
```cpp
class Sales_data {
public:
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &) = default;
    Sales_data() = default;
    ~Sales_data() = default;
}
```
**注意：** 当我们在 类内 用`=default`修改成员声明时，合成的函数将**隐式**地被声明为内联的（就像任何其他类内声明的成员函数一样）。

### 6.2 如果希望 编译器生成合成版本的 拷贝控制成员 不是内联的应该怎么做？
&emsp;&emsp; 当我们在 类内 用`=default`修改成员声明时，合成的函数将**隐式**地被声明为内联的（就像任何其他类内声明的成员函数一样），如果不希望合成的成员是内联的，我们应该只对成员的 类外**定义**使用`=default`：
```cpp
class Sales_data {
public:
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &);
    Sales_data() = default;
    ~Sales_data() = default;
}
Sales_data& Sales_data::operator=(const Sales_data &) = default;
```
在上面的代码中，拷贝控制运算符 不是内联的，其它控制成员都是。

### 6.3 什么类型的 函数可以使用`=default`？
&emsp;&emsp; 我们只能对 具有合成版本的 成员函数 使用`=default`（即 默认构造函数、拷贝控制成员）






&emsp;
&emsp; 
## 7. 阻止拷贝
### 7.1 为什么要阻止拷贝？
&emsp; &emsp; 虽然大部分类应该定义拷贝构造函数和拷贝赋值运算符，但对于某些类来说，这些操作没有任何意义，比如说`iostream`类阻止了拷贝(因为流类型不允许拷贝)，以避免多个对象写入或读取相同的IO缓冲。

### 7.2 如何阻止拷贝
&emsp;&emsp; 为了阻止拷贝，我们似乎应该直接不定义 拷贝控制成员就行了，但如果你不定义，编译器就会帮你生成。
&emsp;&emsp; 如何解决这个问题呢？ 一共有两个方法：
> (1) 在C++11新标准下，我们可以通过将 拷贝构造函数、拷贝赋值运算符定义为 删除的函数(deleted function)来阻止拷贝。
> (2) 将 拷贝构造函数、拷贝赋值运算符 声明为私有，但是不提供定义。
> 

### 7.3 什么是 删除的函数？
&emsp;&emsp; 我们虽然声明了它们，但不能以任何方式使用它们

### 7.4 如何定义 删除的函数？
&emsp;&emsp; 通过在函数的参数列表后面加上`=delete`来指出我们希望该函数被定义为 删除的。

### 7.5 定义一个 NoCopy类，这个类使用 默认构造函数、析构函数用合成的，并阻止拷贝
方法一：用`=delete`
```cpp
class NoCopy {
public:
    NoCopy() = default;
    NoCopy(const NoCopy &)=delete;
    NoCopy & operator=(const NoCopy &) = delete;
    ~NoCopy() = default;
}
```
方法二：将这两个函数声明为私有
```cpp
class NoCopy {
public:
    NoCopy() = default;

    ~NoCopy() = default;
private:
    NoCopy(const NoCopy &);
    NoCopy & operator=(const NoCopy &);
}
```

### 7.6 将函数声明为`=default` 和 `=delete` 时 有何不同？ 为什么？
(1) `=default`既可以和声明一起出现在类的内部，也可以作为定义出现在类的外部，而`=delete`必须出现在函数第一次声明的时候，原因如下：
&emsp;&emsp; 一个默认成员只影响为这个成员生成的代码，因此`=default`直到编译器生成代码的时候才需要；
&emsp;&emsp; 而另一方面，编译器需要知道一个函数是否为删除的，以便从一开始就禁止使用这个被删除的函数
(2) 我们可以对任何函数指定`=delete`，但只能对 具有合成版本的 成员函数 使用`=default`（即 默认构造函数、拷贝控制成员）

### 7.7 哪些函数不能是 删除的成员？为什么？
&emsp;&emsp; 析构函数不能是删除的，如果析构函数被删除了，那就无法销毁此类型的对象了。

### 7.8 如果一个类的析构函数被删除了，会发生什么？
&emsp;&emsp; 析构函数不能被删除，但如果非要将析构函数定义为删除的，那编译器将不允许定义该类型的变量 或 创建该类的临时对象。
&emsp;&emsp; 而且，如果一个类 有某个成员的析构函数是被删除的，我们也不能定义该类型的变量或临时对象，因为如果一个成员的析构函数是被删除的，那该成员就无法销毁，该成员不能被销毁，则整个成员也就无法销毁了。    

### 7.9 如果一个类的析构函数被删除了，是不是就不能创建任何该类的对象了？
&emsp;&emsp; 不是，如果一个类的析构函数被删除了，我们不能定义该类的变量或成员，但我们可以动态分配这种类型的对象，不过我们不能释放这种对象，即不能`delete`它：
```cpp
struct NoDestructor {
    NoDestructor() = default;   // 使用合成默认构造函数
    ~NoDestructor() = delete;   // 删除 析构函数
}

NoDestructor nd; // 错误，不能创建 析构函数被删的的类 的对象
NoDestructor * ptr = new NoDestructor(); // 正确，但我们不能delete ptr
delete ptr;         // 错误，NoDestructor的析构函数是delete的
```

### 7.10 一个类的 合成版本的控制成员 什么时候会是 删除的？为什么？
&emsp;&emsp; (1) 如果 类的某个成员的析构函数 是删除的 或 不可访问的(例如是`private`)，则该类的 合成析构函数 将会是删除的。
> 一个类A的对象在析构时，如果它的成员是的类型为类B，则该成员销毁时会调用这个类（类B）的析构函数对其进行销毁，但类B的析构函数是删除的(或 不可访问的)，这意味着这个成员不能被销毁，成员不能被销毁，则意味着类A的对象不能被销毁，这就导致类A的的析构函数是 被删除的。
> 
&emsp;&emsp; (2) ① 如果 类的某个成员的拷贝构造函数 是删除的或不可访问的(例如是`private`)，则类的合成拷贝构造函数被定义为删除的。② 如果类的某个成员的析构函数是删除的或不可访问的，则类合成的拷贝构造函数也被定义为删除的。
> ① 合成拷贝构造函数的拷贝规则是这样的：对于类类型的成员，调用该类的拷贝构造函数对该成员进行拷贝，但如果 这个成员的拷贝构造函数 是删除的或不可访问的，那就意味着这个成员不可能被 拷贝构造，这个成员不能被拷贝构造，那就意味着含有这个成员的类 不能被 拷贝构造，这就造成 含有这个成员的类 的合成拷贝构造函数被定义为删除的。
> ② 析构函数是删除的，意味着不能 销毁 这个类的对象，所以这个类合成的拷贝构造函数 会被定义为删除的

&emsp;&emsp; (3) 如果类的某个成员的拷贝赋值运算符是删除的或不可访问的，或是类有一个 const成员或引用成员，则类的合成拷贝赋值运算符被定义为删除的。
> 对于类A，它的的合成拷贝赋值运算符 的规则是：如果成员的类型是 类类型B，则调用类B的拷贝赋值运算符。但类B的拷贝赋值运算符是删除的或不可访问的，这就造成类A的 合成拷贝赋值运算符被定义为删除的。
> const对象只能被初始化，不能被赋值，肯定不能直接的拷贝(合成拷贝赋值运算符就是简单的拷贝)，引用对象虽然可以赋值，但是赋值后就改变的是 引用指向的值，而不是引用本身。

&emsp;&emsp; (4) 如果类的某个成员的析构函数是删除的或不可访问的，或是类有一个引用成员，它没有类内初始化器(参见2.6.1节，第65页)，或是类有一个const成员，它没有类内初始化器且其类型未显式定义默认构造函数,则该类的默认构造函数被定义为删除的。
**本质上，这些规则的含义是：** 如果 一个类有成员 不能被默认构造、拷贝、复制 或 销毁，则对应的成员函数将被定义为 删除的。

### 7.11 还有其它方法阻止拷贝吗？
&emsp;&emsp; `=delete`是C++11的新标准，在此之前，类是通过将其拷贝构造函数和拷贝控制运算符声明为`private`来阻止拷贝的：
```cpp
class PrivateNoCopy {
    // 无 访问说明符，意味着下面两个成员函数是private的
    PrivateNoCopy(const PrivateNoCopy&);
    PrivateNoCopy & operator=(const PrivateNoCopy&);
public:
    PrivateNoCopy()=default;
    ~PrivateNoCopy(); // 析构函数虽然可以访问，但是由于拷贝构造函数和拷贝赋值运算符都是priave的，所以用户不能拷贝它们
}
```
在上面的类`PrivateNoCopy`中，由于析构函数是`public`的，所以我们可以定义`PrivateNoCopy`类型的对象，但它的 拷贝构造函数和拷贝赋值运算符都是priave的，所以不能拷贝`PrivateNoCopy`类型的对象。
**但是**，友元和成员函数依然可以拷贝它们。为了阻止友元和成员函数对其拷贝，我们将这些拷贝控制成员声明为`priave`但不定义它们。
**建议：** 若一个类希望组织拷贝，我们应该使用`=delete`来定义该类的  拷贝构造函数和拷贝赋值运算符，而不是将它们声明为`priave`。






&emsp;
&emsp;
## 8. 拷贝控制 和 资源管理
### 8.1 一般来说，定义类的 拷贝语意 有几种选择？
三种：
① 使类的行为看起来像 一个值：
&emsp; &emsp; 当我们拷贝一个像值的对象时，副本和原对象 是完全独立的，改变副本不会影响原对象，反之亦然。比如 标准库容器和`string`类 的行为就像值。
② 使类的行为看起来像 一个指针
&emsp; &emsp; 当我们拷贝一个像指针的对象时，副本和原对象使用相同的底层数据，改变副本将会改变原对象，反之亦然。比如`shared_ptr`提供类似指针的行为。
③ 使类的行为看起来 既不像值 也不像指针：
&emsp; &emsp; 比如 `unique_ptr`和 IO类型 都不允许拷贝或赋值，因此 它们的行为 既不像值 也不像指针。

### 8.2 定义一个类，名为`HasPtr`，它有一个`int`成员，和一个指向动态内存的`string`的指针成员，请分别给与它 不同的拷贝语义
### 8.2.1 行为像值的类`HasPtr`
&emsp;&emsp; 为了给类提供 值 的行为，那对于类管理的资源，每个对象都应该有一份自己的拷贝：
> ① 定义一个 拷贝构造函数 ，它完成`string*`指向内存里的内容的拷贝，而不是简单的拷贝指针；
> ② 合成析构函数并不会帮忙`delete`动态内存，因此需要自己定义一个析构函数来完成动态内存的释放；
> ③ 拷贝赋值运算符 通常组合了析构函数和构造函数的操作：
> &emsp; 析构操作： 析构`=`左侧的对象，
> &emsp; 构造操作： 从右侧拷贝数据到左侧对象
> 因此我们需要自定义一个 拷贝赋值运算符，它先来释放左侧对象的动态内存，然后从右侧对象拷贝数据。
```cpp
class HasPtr {
public:
    // 所有参数都有默认值，因此是个默认构造函数
    HasPtr(const std::string &s=std::string()): i(0), ps(new std::string(s)) { }

    HasPtr(const HasPtr&p): i(p.i), ps(new std::string(p.ps)) { }
    HasPtr& operator=(const HasPtr&);
    ~HasPtr(){delete ps; }
private:
    int i;
    std::string * ps;
};
```
####  8.2.1.1 下面`HasPtr`类的 拷贝赋值运算符的定义 正确吗？
```cpp
HasPtr& HasPtr::operator=(const HasPtr&rhs)
{
    delete ps;  // 释放 =左侧 的动态内存，避免内存泄漏
    ps = new std::string(*rhs.ps);  
    i = rhs.i;
    return *this; // 返回本对象，然后对象绑定到了引用上
}
```
不正确，上面`HasPtr`类的定义乍一看好像完全没有问题，但我们来看下面的代码：
```cpp
HasPtr demo("Hello World");
demo = demo;
```
在上面的代码中，我们将`demo`对象赋给了它自身，看起来好像没有问题，但上面定义的 拷贝赋值运算符 先执行了`delete ps`，这就意味着后面的 `ps = new std::string(*rhs.ps)`的行为是未定义的，因为我们已经在第一步就释放了动态内存，**也就是说上面的 拷贝赋值运算符 不能处理 将一个对象赋予他自身的情况。**
####  8.2.1.2 如何解决呢？
&emsp;&emsp;当你编写一个 拷贝赋值运算符 时，一个好的模式是先将右侧运算对象拷贝到一个局部临时对象中，当拷贝完成后再销毁左侧运算对象的现有成员，这样就安全了，一旦左侧运算对象的资源被销毁，此时将数据从临时对象拷贝到左侧对象即可：
```cpp
HasPtr& HasPtr::operator=(const HasPtr&rhs)
{
    std::string *tmp =  new std::string(*rhs.ps);
    delete ps;  // 释放 =左侧 的动态内存，避免内存泄漏
    ps = tmp;  // 将临时对象赋给它
    i = rhs.i;
    return *this; // 返回本对象，然后对象绑定到了引用上
}
```
### 8.2.2 行为像指针的类`HasPtr`
&emsp;&emsp; 为了给类提供 指针 的行为，
> ① 我们要为其定义拷贝构造函数和拷贝赋值运算符，用来拷贝成员本身；
> ② 我们还需要定义析构函数来释放内存，但不能简单的释放内存，而是要使用引用计数，只有当最后一个指向string的HasPtr销毁时，它才可以释放string。

#### 8.2.2.1 如何实现引用计数？
&emsp;&emsp; 如何保证所有指向相同资源的对象 共享同一 计数器 呢？比如对于下面的代码：
```cpp
HasPtr p1("Hello World");
HasPtr p2(p1);
HasPtr P3(p1);
```
在上面的代码中，`p1、p2、p3`都指向相同的资源，但问题是在我们创建`p3`的时候我们如何递增p2的计数器呢？**解决这个问题的办法是**： 我们可以将计数器放在 动态内存中，在拷贝或赋值的时候，我们只拷贝指向计数器的指针，这样副本和原对象都会指向相同的计数器。
#### 8.2.2.2 拷贝控制成员、构造函数 怎么操作引用计数？
| 函数                         | 需要承担的工作                     |
| ---------------------------- | ----------------------------------- |
| 构造函数(除了拷贝构造函数) | ① 初始化对象；②创建一个引用计数，用来记录有多少个对象与正在创建的对象共享相同的资源，这个引用计数应该被初始化为1 |
| 拷贝构造函数                 | 不分配计数器，而是拷贝给定对象的数据成员，在拷贝计数器时只拷贝指针，并将计数器加1，这样就能做到和给定对象共享同一计数器。                 |
| 析构函数                     | 递减计数器，并在计数器变为0的时候释放资源。                     |
| 拷贝赋值运算符               | ① 递增`=`左侧对象的引用计数； ②递减`=`右侧对象的引用计数，并在其引用计数为0的时候释放资源                                                     |
#### 8.2.2.3 类的定义
```cpp
class HasPtr {
public:
    HasPtr(const string s = string()): ps(new string(s)), i(0), use(1) { }
    HasPtr(const HasPtr &p): ps(p.ps), i(p.i), use(p.use) { ++*use; }
    HasPtr& operator=(const HasPtr &);
    ~HasPtr();
private:
    string * ps;
    int i;
    size_t * use;
}
```
**析构函数：**
&emsp;&emsp; 对于析构函数，行为像指针的类就不能无条件释放资源了，而是应该先递减引用计数，然后只有在 引用计数变为0 的时候才释放资源：
```cpp
HasPtr::~HasPtr()
{
    if(--*use == 0){
        delete ps;
        delete use;
    }
}
```
**拷贝赋值运算符：**
&emsp;&emsp; 对于拷贝赋值运算符，我们首先要保证 自赋值 可以正常处理，在这里我可以通过 先递增`=`右侧对象的引用计数来做到
```cpp
HasPtr& HasPtr::operator=(const HasPtr &rhs)
{
    ++*rhs.use;
    // 如果是自赋值，因为前面 ++*rhs.use 已经将引用计数加1了，所以自赋值时肯定走不到下面这个 if分支
    if(--*use == 0){
        delete ps;
        delete use;
    }
    i = rhs.i;
    ps = rhs.ps;
    use = rhs.use;
    return *this;
}
```






&emsp;
&emsp;
## 9. 交换操作
### 9.1 什么情况下 要为自己的类定义 交换操作？
&emsp;&emsp; 对于那些和 标准库中的重排元素顺序的算法 一起使用的类，为其定义`swap()`是非常重要的，因为这些排序算法 在交换两个元素的顺序时 会用到`swap()`。
&emsp;&emsp; 假如类没有定义`swap()`，那么算法将使用 标准库定义的`swap()`；如果类定义了`swap()`，那么算法将使用 自定义的`swap()`；

### 9.2 交换操作 是必须的吗？
&emsp;&emsp; 和拷贝控制成员不一样，交换操作 并不是必要的，而是**作为一种优化手段**。

### 9.3 为上面的 类值版本的`HasPtr` 定义一个`swap()`
类定义如下：
```cpp
class HasPtr {
public:
    // 所有参数都有默认值，因此是个默认构造函数
    HasPtr(const std::string &s=std::string()): i(0), ps(new std::string(s)), { }

    HasPtr(const HasPtr&p): i(p.i), ps(new std::string(p.ps)), { }
    HasPtr& operator=(const HasPtr&);
    ~HasPtr(){delete ps; }
private:
    int i;
    std::string * ps;
};

HasPtr& HasPtr::operator=(const HasPtr&rhs)
{
    std::string *tmp =  new std::string(*rhs.ps);
    delete ps;  // 释放 =左侧 的动态内存，避免内存泄漏
    ps = tmp;  // 将临时对象赋给它
    i = rhs.i;
    return *this; // 返回本对象，然后对象绑定到了引用上
}
```
#### 9.3.1 使用 拷贝控制成员 实现 `swap()`
```cpp
swap(HasPtr &lhs, HasPtr &rhs)
{
    HasPtr tmp = lhs;   // 使用了 HasPtr的 拷贝构造函数
    lhs = rhs;          // 使用了 HasPtr的 拷贝赋值运算符
    rhs = tmp;          // 使用了 HasPtr的 拷贝赋值运算符
}
```
#### 9.3.2 改进的版本
&emsp;上面的代码完全没问题，但是效率并不是很高：它将`lhs`中的`string`拷贝了两次：
&emsp;&emsp; 第一次：`HasPtr tmp = lhs;`
&emsp;&emsp; 第二次：`rhs = tmp;`
而且拷贝一个类值的`HasPtr`会分配一个新的string并将其拷贝到`HasPtr`指向的位置，而理论上这些内存分配都是可以避免的：
```cpp
class HasPtr {
friend void swap(HasPtr &, HasPtr &);
public:
    /*
        公有成员
    */
private:
    /*
        私有成员
    */
};

inline
void swap(HasPtr &rhs, HasPtr &lhs)
{
    using std::swap;
    swap(rhs.ps, lhs.ps);
    swap(rhs.i, lhs.i);
}
```

### 9.4 调用 `swap`时需要注意什么？
&emsp;&emsp; 每个swap调用都应该是未加限定的，也就是说在调用`swap函数`时 我们应该使用`using std::swap`，而不是通过在前面加上 `std::`来强制指定 swap的版本。下面我们实例来解释一下这句话：
```cpp
inline
void swap(HasPtr &rhs, HasPtr &lhs)
{
    std::swap(rhs.ps, lhs.ps) // 强制使用 标准库版本的swap
    std::swap(rhs.i, lhs.i);
}
```
而是应该使用`using std::swap`：
```cpp
inline
void swap(HasPtr &rhs, HasPtr &lhs)
{
    using std::swap;
    swap(rhs.ps, lhs.ps);
    swap(rhs.i, lhs.i);
}
```
上面两个`HasPtr`类的实现好像都一样，因为交换的是 指针和`int`，而他们都是内置类型，内置类型是没有自己的`swap`操作的，肯定会使用`std::swap`，不过这两个实现的效果是一样的，但下面这个就不一样了：
```cpp
class Foo {
friend void swap(Foo &, Foo &);
public:
    /* 省略...*/
private:
    HasPtr hp;
    /* 省略...*/
};
```
上面的代码中，我们定义了`Foo类`，它含有一个`HasPtr类型`的成员，下面我们来为`Foo类`提供`swap`操作的定义：
```cpp
void swap(Foo &rhs, Foo &lhs)
{
    std::swap(rhs.hp, lhs.hp); // 使用的是 标准库版本的swap
    /* 交换其它成员 */
}
```
#### 9.4.1 上面的代码有什么问题？
&emsp;&emsp; 在上面`swap`操作的定义中，我们直使用的是 标准库版本的swap，而不是`HasPtr类`自己的swap函数，`std::swap(rhs, lhs)`这个语句以及直接指定了swap的版本。
&emsp;&emsp; 其实也是说这样会编译不过，只不过这样做就浪费了我们之前为`HasPtr类`定义的swap函数
#### 9.4.2 正确的做法是怎样的？
&emsp;&emsp; 使用using命令：
```cpp
void swap(Foo &rhs, Foo &lhs)
{
    using std::swap;
    swap(rhs.hp, lhs.hp); // 使用的是 标准库版本的swap
    /* 交换其它成员 */
}
```
**上面的代码是怎么运行的？**
&emsp;&emsp; 虽然我们在前面使用了`using std::swap;`，但`swap(rhs.hp, lhs.hp);`使用的是 `HasPtr类`自己的swap函数，而不是`std::swap;`，因为如果存在类型特定的`swap`版本，其匹配程度会优于`std:`中的版本(具体原因要在 16.3节(P616)才能讲到)。还有一个问题就是``using std::swap;`为什么没有隐藏 `HasPtr类`自己的swap函数，起哄原因要在 18.2.3节(P706)才能讲到。
#### 9.4.3 总结
&emsp;&emsp; 也就是说我们不应该直接指定用哪个版本的swap函数，而是应该交给编译器决定，它会优先匹配我们自己定义的 swap函数(如果有的话)，然后才是标准库的swap函数(即`std::swap`)

### 9.5 如何在赋值运算符中使用`swap`？
代码如下：
```cpp
HasPtr & operator=(HasPtr rhs)
{
    swap(*this, rhs);
    return *this;
}
```
注意： `rhs`参数不是引用，我们将`=`右侧运算对象以传值的方式传递给了赋值运算符，因此`rhs`是右侧对象的一个副本。
## 9.5.1 使用`swap` 定义 拷贝赋值运算符 有什么优点？
&emsp;&emsp; 自动处理了自赋值的情况，因为`rhs`是右侧对象的一个副本；






&emsp;
&emsp;
## 10. 拷贝控制成员 的其它用途
### 10.1 定义 拷贝控制成员 有哪些原因？
(1) 资源管理，这是最主要的原因；
(2) 用来实现 登记功能

### 10.2 设计一个`Message`类和`Folder`类，它要完成下列功能：
&emsp;&emsp; `Message`类和`Folder`类 分别表示 电子邮件消息 和 消息目录，每个 Message对象 可以出现在多个Folder中，但是任意给定的 Message 只能有一个副本，这样一条Message被改变，则从它所在的每一个Folder中都能看到变更后的内容。
### 10.2.1 各个函数怎么设计？
函数 | 需具备的功能 | 
---------|----------|
 save() | 向给定folder中添加Message |
 remove() | 向给定folder中删除Message |
 拷贝构造函数 | 新构造的对象 应该和 给定的对象 相独立，但是出现在相同的folder中 |
 拷贝赋值运算符| 和往常一样，拷贝赋值运算符 也要执行释放资源的操作(从包含左侧对象的folder删除该message对象，而且要能处理自赋值 | 
 析构函数 | 从每个包含该Message对象的folder中删除该对象 |
&emsp;我们可以看到：
&emsp;&emsp; ① 拷贝构造函数 和 拷贝赋值运算符 都要将message对象添加到 一组folder中；
&emsp;&emsp; ② 析构函数 和 拷贝赋值运算符 都要将从 一组folder中 删除message对象；
那我们可以分别为 将message对象添加到 一组folder中 和 从一组folder中删除message对象 写一个函数来实现代码重用，但我们应该将这两个函数实现为`private`，这样安全一点。
&emsp;&emsp; 另外，`Message`类 和 `Folder`类 应该互为友元，这样才能做到 互相使用对方的私有成员。
### 10.2.2 如何处理自赋值？
&emsp;&emsp; 先从包含 左侧对象 的folder中 删除它，这样就能处理自赋值了
```cpp
class Message {
    friend class Folder; // 将 Folder声明为友元类，这样该类的成员就能访问 Message类的私有成员了。
public:
    // 注意， 这是个默认构造函数！因为 该构造函数中 所有形参都有默认值。
    explicit Message(const string &str = ""): contents(str) { }
    Message(const Message &);
    Message& operator=(const Message&);
    ~Message();
    void save(Folder &);
    void remove(Folder &);
private:
    string contents;
    set<Folder*>folders;
    void add_to_folders(const Message&);
    void remove_from_folders();
};

void Message::save(Folder &f)
{
    folders.add(&f);
    Folder.addMsg(*this);
}

void Message::remove(Folder &)
{
    folders.erase(&f);
    Folder.remMsg(*this);
}

void Message::add_to_folders(const Message&m)
{
    for(auto f : m.folders) // 这里的 范围for循环中 f不需要声明为引用，因为我们不需要改变 m.folders 的值
        f->addMsg(this); // 注意，添加的是指针。
}

// 注意这个拷贝构造函数的写法！
Message::Message(const Message &m):contents(m.contents), folders(m.folders)
{
    add_to_folders(m);
}

void Message::remove_from_folders()
{
    for(f : folders)
        f->remMsg(this);
}

Message::~Message()
{
    remove_from_folders();
}

// 和往常一样，拷贝赋值运算符 也要执行释放资源的操作，而且要能处理自赋值。
Message& Message::operator=(const Message &rhs)
{
    remove_from_folders(); // 先从包含 左侧对象 的folder中 删除它，这样就能处理自赋值了
    contents = rhs.contents;
    folders = rhs.folders;
    add_to_folders(rhs);
    return *this;
}
``` 
### 10.2.3 如何为 `Message类` 定义 swap操作？
(1) 先将包含 两个Message的folder将其删除
(2) 开始交换（`Message类`的包含的string成员和set成员都有自己的swap版本）
(3) 将 Message对象 添加到 包含它们的folder中
```cpp
void swap(Message &lhs, Message &rhs)
{
    using std::swap; // 这里其实可有可无，但是每次都加上总没错
    // 先将包含 两个Message的folder将其删除
    for(auto f : lhs.folders)
        f->remMsg(&lhs);
    for(auto f : rhs.folders)
        f->remMsg(&rhs);

    swap(lhs.contents, rhs.contents);
    swap(lhs.folders, rhs.folders);

    for(auto f : lhs.folders)
        f->addMsg(&lhs);
    for(auto f : rhs.folders)
        f->addMsg(&rhs);
}

```
?TODO: `folder类`还没定义，答案在习题册里面有，做习题的时候记得加上






&emsp;
&emsp;
## 11. 实现一个 动态内存管理类
&emsp;&emsp; 某些类需要在运行时分配可变大小的内存空间，这种类通常使用标准库容器来保存它们的数据，比如vector。某些类需要自己进行内存管理，这些类一般来说必须定义自己的拷贝控制成员来管理所分配的内存。
&emsp;&emsp; 但是这一策略并不是对每个类都适用：某些类需要自己进行内存分配。这些类一般来说必须定义自己的拷贝控制成员来管理所分配的内存。
我们将实现`标准库vector`的一个简化版本，名为`StrVec类`，该类只用于`string`。
&emsp;&emsp; `StrVec类`将使用一个allocator来获取原始内存，由于allocator获取的原始内存是未构造的，我们将在需要添加新元素时使用`constructor`在原始内存中创建对象，在删除元素时使用`destory`销毁元素。每个`StrVec`有三个指针成员指向其元素使用的内存：
> elements：指向分配的内存中的首元素
> first_free：指向最后一个实际元素之后的位置
> cap：指向分配的内存末尾之后的位置

### 11.1 类的定义
```cpp
class StrVec{
public:
    StrVec():{} elements(nullptr), elements(nullptr), cap(nullptr)  { }
    StrVec(const StrVec &);
    StrVec& operator=(const StrVec&);
    ~StrVec();

    // 对于不改变调用对象的函数，应该声明为 const成员函数
    size_t size() const { return first_free - elements;}
    size_t capacity() const { return cap - elements ;} 
    string * begin() const {return elements;}
    string * end() const {return first_free;}
    void push_back(const string *);
private:
    string * elements;
    string * first_free;
    string * cap;
    static allocator<string> alloc;
    void check_n_alloc(){ if(capacity() == size()) reallocate(); }
    void reallocate();
    void free();
    pair<string*, string*> alloc_n_copy(const string *, const string *);
};
```
### 11.2 为什么`alloc成员变量`要被声明为`static成员变量`？
&emsp;&emsp; `static成员变量`不属于对象，而是属于类本身，也就是说所有`StrVec`对象共享同一个`alloc成员变量`。
&emsp;&emsp; `alloc成员变量` 是 `allocator对象`，通过 `allocator对象`我们可以申请未构造的内存，我们可以通过同一个`allocator对象`来申请内存，而不同对象申请的内存是互相独立的，因此可以让所有 `StrVec`对象 共用同一个 `allocator对象` 来申请内存。

### 11.3 const成员函数
&emsp;&emsp; 对于不改变调用对象的函数，应该声明为 const成员函数：
```cpp
size_t size() const { return first_free - elements;}
size_t capacity() const { return cap - elements ;} 
string * begin() const {return elements;}
string * end() const {return first_free;}
```

### 11.4 `push_back()`的实现
`push_back()`的实现需要保证以下方面：
> 确保有空间可插入：这里用已在类内实现的`check_n_alloc()`函数即可；
> 因为我们使用的是`allocator类`，因此目标内存是未构造的，我们需要用`alloc.construct`进行构造。
```cpp
void StrVec:: push_back(const string *str)
{
    // 插入之前先确保有空间可插入
    check_n_alloc();
    // first_free++ ：先在first_free指向的位置构造元素，然后递增first_free指针让它指向下一个位置。
    alloc.construct(first_free++, str);
}
```
### 11.5 `alloc_n_copy()`的实现
&emsp;&emsp; 此函数只会在 拷贝构造函数 和 拷贝赋值运算符 中使用，此时还没调用`alloc.allocate()`分配内存，因此需要在`alloc_n_copy()`内部先请求分配足够的内存 再从原对象中拷贝。
```cpp
pair<string*, string*> StrVec::alloc_n_copy(const string *s, const string *e)
{
    auto data = alloc.allocate(e - s);
    auto end = uninitialized_copy(s, e, data); // end指向的是 最后一个元素的后面
    return {data, end};
}
```

### 11.6 `free()`的实现
`free()`的任务是释放内存，因为内存是通过`allocator类`分配的，因此`free()`有两个工作：
> ① `destroy()`所有的元素
> ① `deallocate()`分配的内存

另外，，因此我们还需要 防止 `elements`为空的情况：若`elements`为空，则我们什么也不做：
```cpp
void StrVec::free()
{
    if(elements == NULL){
        auto p = first_free;
        while(p != elements)
            alloc.destroy(--p);
        alloc.deallocate(elements, cap - elements);
    }
}
```
### 11.7 拷贝控制成员
#### 11.7.1拷贝构造函数

```cpp
StrVec::StrVec(const StrVec&rhs)
{
    auto ret = alloc_n_copy(rhs.elements, rhs.first_free);
    elements = ret.first;
    first_free = ret.second;
    cap = ret.second;
}
```
#### 11.7.2 拷贝赋值运算符 如何处理自赋值？
&emsp;&emsp; 先使用`alloc_n_copy()`，然后再free，最后再将`alloc_n_copy()`的返回值赋给 成员即可。
```cpp
StrVec& StrVec::operator=(const StrVec & rhs)
{
    auto ret = alloc_n_copy(rhs.elements, rhs.first_free);
    free();
    elements = ret.first;
    first_free = ret.second;
    cap = ret.second;    
    return *this;
}
```

### 11.8 `reallocate()`的实现
#### 11.8.1 `reallocate()`需要完成功能
在编写reallocate函数之前，我们思考一下它的功能：
> ① 为一个新的、更大的string数组分配内存
> ② 在内存空间的前一部分构造对象，保存现有元素
> ③ 销毁原内存空间中的元素，并释放这块内存
> 
#### 11.8.2 如何提高性能？
**但这会带来一个问题**:
&emsp;&emsp; 为一个string数组重新分配内存会引起从就内存空间到新内存空间逐个拷贝string的问题。（因为string类具有类值行为，当拷贝一个string时，新老string是相互独立的）在重新分配内存空间时，如果我们能够避免分配和释放string的额外开销，那么StrVec的性能就会好很多。
&emsp;&emsp; 在C++11标准中，有一些标准库类（包括string）定义了 **“移动构造函数”**，该函数将资源从给定对象“移动”而不是拷贝到正在创建的对象。
**但什么是 “移动构造函数” 呢？**
&emsp;&emsp; 我们可以这么理解：就拿string类来说，假设每个string都有一个指向char数组的指针，可以假定string的移动构造函数进行了指针的拷贝，而不是为字符分配内存空间然后拷贝字符。
**如何使用标准库容器的 移动构造函数 呢？**
&emsp;&emsp; 我们可以使用名为`move`的标准库函数，它定义在`utility`头文件中。
```cpp
void StrVec::reallocate()
{
    size_t new_capacity = size() ? 2*size() : 1;
    auto new_data = alloc.allocate(new_capacity);
    auto dest = new_data;
    auto elem = elements;
    while(elem != first_free)
        alloc.construct(dest++ ,std::move(*elem++));
    free();
    elements = new_data
    first_free = dest
    cap = new_data + new_capacity;       
}
```






&emsp;
&emsp;
## 12. 对象移动
### 12.1 为什么要引入 对象移动？
**(1) 提高效率**
&emsp;&emsp; 我们知道，很多情况下都会发生对象拷贝，**而在某些情况下，对象在拷贝后立即就被销毁了，在这种情况下如果能移动而不是 先拷贝后销毁对象 将大幅度提高性能**，我们之前写的`StrVec::reallocate()`就是一个很好的例子：在重新分配内存时，将原内存中的对象 “移动” 到新内存，而不是先拷贝到新内存中，然后再释放。
**(2) 方便使用不能拷贝的对象**
&emsp;&emsp; 像 `IO`、`unique_ptr` 这样的类都包含不能被共享的资源，因此这些类型的对象不能拷贝但是可以移动。
**(3) 可以用标准库容器保存不可拷贝的类型**
&emsp;&emsp; 在旧版本的标准库中，容器中保存的类 必须是 可拷贝的，但新标准中，我们可以在 容器中 保存不可拷贝的类型，只要它们能被移动。
**注意：** 标准库容器中，`string`、 `shared_ptr` 和 标准库容器 既可以 移动 也可以拷贝，但是 `IO`、`unique_ptr` 只能移动 不能拷贝。






&emsp;
&emsp;
## 13. 左值 和 右值
**见 “一些语法的总结” 文件夹的笔记**






&emsp;
&emsp;
## 14 移动语义 和 完美转发
**见 “一些语法的总结” 文件夹的笔记**






&emsp;
&emsp;
## 15 左值引用(lvalue References) 和 右值引用(Rvalue References)
**见 “一些语法的总结” 文件夹的笔记**






&emsp;
&emsp;
## 16 标准库`move()`函数
### 16.1 `move()`的工作原理
&emsp;&emsp; `move()`调用告诉编译器：我们有一个左值，但我们希望像一个右值一样使用它。但我们必须意识到的是，调用`move()`就意味着承诺：除了 对`rr1`赋值 或 销毁它 以外。我们将不再使用它，也就是说在调用`move()`之后，我们不能对移后源对象的值做任何假设。

### 16.2 使用 `move()`函数时需要注意什么？
&emsp;&emsp; 使用 `move()`函数时应该使用 `std::move()`，而不是使用'using std::move()`，这样可以避免潜在的名字冲突。






&emsp;
&emsp;
## 17 移动构造函数 和 移动赋值运算符
### 17.1 移动构造函数(移动赋值运算符) 和 拷贝构造函数(拷贝赋值运算符) 有何区别？
**(1) 工作原理的区别**
&emsp;&emsp; 这个两个成员类似于对应的拷贝操作，但它们是从 给定对象中 “窃取” 资源 而不是拷贝资源。
**(2) 函数原型的区别**
&emsp;&emsp; 类似于对应的拷贝操作，它们第一个参数都是该类类型的一个引用，但不同于拷贝构造函数的是，这个引用参数在 移动构造函数(移动赋值运算符) 中是一个 右值引用，与 拷贝构造函数 一样，移动构造函数中的任何额外的参数都必须有默认实参。
&emsp;&emsp; 移动操作第一个参数都是该类类型的一个 右值引用，但该右值引用不能是`const`的，因为我们会改变这个参数。
**(3) 需完成的任务的区别**
&emsp;&emsp; 除了移动对象外，移动构造函数还必须确保移后源对象处于这样一种状态：销毁它是无害的，特使是在资源完成移动后，源对象必须不再指向被移动的资源，这些资源的所有权应该归属新创建的对象。
**(4) 内存分配上的区别**
&emsp;&emsp; 和拷贝构造函数不一样的是，移动构造函数 不分配任何新内存，它只是接管源对象的内存。

### 17.2 移动构造函数 需要完成哪些任务？
有下列任务：
> (1) 移动资源（一般通过 构造函数初始化列表完成）；
> (2) 在完成移动后，源对象必须不再指向被移动的资源。
> 

### 17.3 为 `StrVec类` 定义移动构造函数
```cpp
// 任务1：通过构造函数初始化列表完成 资源移动
StrVec::StrVec(const StrVec &&s) noexcept:elements(s.elements),first_free(s.first_free), cap(s.cap)
{   
    // 任务2：完成移动后，源对象必须不再指向被移动的资源
    s.elements = s.first_free = s.cap = nullptr;
}
```

### 17.4 在上面定义的`StrVec类` 的 移动构造函数中，若忘了让 源对象 不再指向被移动的资源，会发生什么？
&emsp;&emsp; 因为 源对象 最终肯定会被销毁，因此会调用析构函数释放内存，如果我们忘记了 将`s.elements`置为`nullptr`，那么`s.elements`指向的内存将会被释放，也就是说 我们刚刚移动好的内存会被释放掉。

### 17.5 `noexcept`是做什么用的？
&emsp;&emsp; `noexcept`是在C++11中加入的，它的作用是 通知标准库该函数数不抛出任何异常。
换句话说，`noexcept`是我们承诺一个函数不抛出异常的一种方法。

### 17.6 如何使用 `noexcept` ？
&emsp;&emsp; 我们在一个函数的参数列表后指定`noexcept`。
&emsp;&emsp; 对于构造函数，`noexcept`应该出现在 参数列表 和 初始化列表 之间，而且必须同时在 类内声明中 和 类外定义中都指定`noexcept`：
```cpp
class StrVec {
public:
    StrVec(StrVec &&) noexcept;
/*
    其它成员
*/
};
StrVec::StrVec(StrVec &&) noexcept: elements(s.elements),first_free(s.first_free), cap(s.cap)
{   
    s.elements = s.first_free = s.cap = nullptr;
}
```

### 17.7 `noexcept`有什么好处？
&emsp;&emsp; 对于一个函数，标准库 默认该函数有可能抛出异常，并且为了处理这种可能性会做一些额外的工作。
&emsp;&emsp; 因此，通过 `noexcept` 告诉 标准库我们的函数不会抛出异常 将会提高性能。

### 17.8 为什么在定义 移动构造函数 和 移动构造函数 时要使用 `noexcept` ？
**(1) 提高性能**
&emsp;&emsp; 因为移动构造函数 和 移动构造函数 都不会分配任何内存，因此移动操作通常不会抛出任何异常。在编写 不会抛出异常 的操作时，我们应该通过指明`noexcept`通知标准库，这样标准库就不会为处理这种可能性会做一些额外的工作，从而提高性能。
**(2) 告诉标准库可以放心大胆的使用 移动构造函数**
&emsp;&emsp; 我们都知道，当对一个`vector`调用`push_back()`时可能会要求为`vector`重新分配内存空间，当重新分配`vector`的内存时，`vector`会将元素从旧空间转移到新内存中。在转移元素时，`vector`必须保证以下要求：
>`vector`必须保证在我们调用`push_back()`时若发生异常，`vector`自身不会发生改变。
>
`vector`在从旧空间转移元素到新空间时有两个选择：调用 拷贝构造函数 或 移动构造函数，但假如在移动元素的过程中抛出了异常，就会存在这样的问题：
> **移动构造函数**：在移动的过程中发生了异常就会造成这样一种局面：旧空间中被移动的元素已经被改变了，而新空间中国未构造的元素却尚且不存在，在这种情况下，`vector`将不能满足自身保持不变的要求。
> **拷贝构造函数**：若在搬运旧元素时使用 拷贝构造函数 就不一样了，因为旧空间的元素并没有被改变，因此`vector`将很容易恢复到移动之前模样：直接释放新分配的内存即可，`vector`中原来的元素依然保持不变。
> 
&emsp;&emsp; 为了避免这种潜在的问题，除非`vector`知道元素类型的移动构造函数不会抛出异常，否则在重新分配内存的过程中，他就必须使用拷贝构造函数，而不是 移动构造函数。
&emsp;&emsp; 若希望在`vector`重新分配内存这类情况下对我们自定义的对象进行移动而不是拷贝，就必须显示地告诉标准库我们的移动构造函数可以安全的使用。我们通过将 移动构造函数(移动赋值运算符) 标记为`noexcept`来做到这一点。

### 17.9 为 `StrVec类` 定义 移动赋值运算符
#### 17.9.1 需要注意哪些？
**需要注意的是**： 
(1) 移动赋值运算符返回的是 自身类类型的 左值引用；
(2) 要标记为 `noexcept`;
(3) 要处理号自赋值
(4) 要负责释放`=`左侧的资源
#### 17.9.2 定义
```cpp
StrVec& StrVec::operator=(StrVec &&rhs) noexcept
{
    if(this != &rhs) // 通过比较地址来判断是否自赋值
        free(); // 先释放左侧资源
        elements = s.elements
        first_free = s.first_free
        cap = s.cap;
        s.elements = s.first_free = s.cap = nullptr;
    }
    return *this;
}
```

### 17.10 移后源对象 应该是怎样的？
需要保证两点：
>(1) **需要保证 移后源对象 是可析构的**：
>&emsp;&emsp; 从一个对象移动数据后并不会销毁该对象，但有时希望移动操作完成后，源对象会被销毁。因此我们在编写一个移动操作时，必须保证  移后源对象 进入一个可析构的状态。
>(2) **需要保证对象仍然是有效的**:
>&emsp;&emsp; 除了将移后源对象置为析构安全的状态外，移动操作还必须保证对象仍然是有效的。
>&emsp;&emsp; 一般来说，对象有效就是指可以安全地为其赋予新值或可以安全地使用而不依赖当前的值。另一方面，移动操作对移后源对象中留下的值没有任何要求，因此，程序不应该依赖移后源对象中的数据。
>
&emsp;&emsp; 例如，当我们从一个标准库`string`或容器对象移动数据时，我们知道以后源对象仍然有效，因此我们可以对该`string`对象执行`empty()`、`size()`等操作，但我们不知道这些操作会返回什么结果：可能返回0，但也可能不是0.
&emsp;&emsp; 再比如说，我们为`StrVec类`定义的 移动操作 将源对象的 `elements、first_free、cap`成员都置为了`nullptr`，这相当于默认初始化了一个`StrVec对象`。但对于其他的类，它们可能表现出完全不同的行为。
**总结**：移动操作之后，移后源对象必须保持有效的、可析构的状态，但是用户不能对其值有任何假设。

### 17.11 合成的 移动操作
#### 17.11.1 在什么情况下 编译器会生成合成版本的 移动操作？
&emsp;&emsp; 只有在一个类 没有定义任何自己版本的拷贝控制成员，且类的每个 非static成员 都可以移动构造 或 移动赋值运算符 时，编译器才会生成合成版本的 移动构造函数 或 移动赋值运算符。
#### 17.11.2 合成版本的移动操作 的工作原理是？
内置类型：直接移动
类类型：如果该类类型有对应的移动操作，编译器也能移动这个成员
#### 17.11.3 如何显示要求编译器生成 合成版本的移动操作？
和其它拷贝控制操作一样，使用`=default`即可：
```cpp
struct Demo {
    Demo() = default;
    Demo(Demo&&) = default; // 显式要求编译器生成 合成移动构造函数
    Demo& operator=(Demo&&) = default; // 显式要求编译器生成 合成移动赋值运算符
};

```
#### 17.11.4 如何使用  合成版本的移动构造函数？
使用`std::move()`函数：
```cpp
// the compiler will synthesize the move operations for X and hasX
struct X {
    int i; // built-in types can be moved
    std::string s; // string defines its own move operations
};

struct hasX {
    X mem; // X has synthesized move operations
};

X x, x2 = std::move(x); // uses the synthesized move constructor
hasX hx, hx2 = std::move(hx); // uses the synthesized move constructor
```
#### 17.11.5 什么时候 合成版本的移动操作 会是删除的？ 
&emsp;如果我们通过`=default`来显式的要求编译器生成合成版本的移动操作，且编译器不能移动所有成员，则编译器会将移动操作定义为删除的函数：
> &emsp;&emsp;与拷贝构造函数不同，移动构造函数被定义为删除函数的条件是：有类成员定义了自己的拷贝构造函数且未定义移动构造函数（因为编译器不会为 定义了 自己版本的拷贝控制成员 的类 合成移动控制操作），或者是有类成员未定义自己的拷贝构造函数并且编译器也不能为其合成移动构造函数。移动赋值运算符的情况类似。
> &emsp;&emsp;如果有类成员的移动构造函数或移动赋值运算符被定义为删除的或者是不可访问的，则类的移动构造函数或移动赋值运算符被定义为删除的。
> &emsp;&emsp;类似拷贝构造函数，如果类的析构函数被定义为删除的或不可访问的，则类的移动构造函数被定义为删除的。
> &emsp;&emsp;类似拷贝赋值运算符，如果类的成员是 const 或是引用类型，则类的移动赋值运算符被定义为删除的。
>
**上面的规则总结一下就是**：
&emsp;&emsp; 如果你通过`=default`来显式的要求编译器生成 合成版本的移动操作，但是你有成员不能被移动，这个时候 该合成版本的移动操作 会被定义为删除的。
来看一个例子，其中`Y`是一个 定义了自己的拷贝成员但是没有定义自己的移动构造函数 的类：
```cpp
struct hasY {
    hasY() = default;
    hasY(hasY&&) = default; // 显式要求编译器生成 合成拷贝构造函数
    Y mem;  // Y没有 移动构造函数
};
hasY hy, hy2 = std::move(hy); // 错误: hasY类 的移动构造函数是被删除的，因为 mem成员 不能被移动。
```
#### 17.11.6 定义了 移动操作 的类 需要注意什么？
&emsp;&emsp; 如果一个类定义了一个 移动构造函数 或 移动赋值运算符，则该类的 合成拷贝构造函数 和 合成拷贝运算符 会被定义为删除的，因此对于定义了自己 移动操作 的类，也必须定义自己 自己的拷贝操作。

### 17.12 如果一个类既有 移动构造函数(移动赋值运算符)，也有 拷贝构造函数(拷贝赋值运算符)，编译器如何决定使用哪个？
&emsp;&emsp; 如果一个类既有移动构造函数，也有拷贝构造函数，**编译器使用普通的函数匹配规则来确定使用哪个构造函数**，赋值操作的情况类似。
&emsp;&emsp; 就拿`StrVec`类来说：
>`StrVec`的**拷贝**构造函数 接收一个`const StrVec&`，因此它可以用于任何可以转换为`StrVec`的类型
>`StrVec`的**移动**构造函数 接收一个`StrVec&&`，因此它只能用于实参是右值的情形
```cpp
StrVec v1, v2;
v1 = v2; // v2 是一个左值，因此使用 拷贝赋值运算符
StrVec getVec(istream &); // getVec函数 返回一个右值
v2 = getVec(cin); // 因为getVec(cin) 返回的是右值，因此这里用的是 移动赋值运算符
```
在上面的代码中有两次赋值：
第一次赋值：`v1 = v2`
&emsp;&emsp; 因为`v2`的类型是`StrVec`，也就是说`v2`是一个左值，因此移动版本的赋值运算符是不可行的，因为我们不能隐式地将一个右值绑定到一个左值，因此这里使用的是 拷贝赋值运算符。
第二次赋值：`v2 = getVec(cin);`
&emsp;&emsp; 在这个赋值中，`getVec(cin)`的返回值是一个右值。在这个赋值中，拷贝赋值运算符 和 移动赋值运算符 都可以用，因为 我们可以将隐式的将一个右值绑定到左值上。 但调用拷贝赋值运算符 需要进行一次`const`的转换，而`StrVec&&`则是精确匹配，因此这里会使用 移动赋值运算符。
**总结：左值拷贝，右值移动。**

### 17.13 有 拷贝构造函数(拷贝赋值运算符)，但没有 移动构造函数(移动赋值运算符)，调用`std::move`会发生什么？
&emsp;&emsp; 首先需要明确的是，如果一个类 有拷贝构造函数 但未定义移动构造函数，此时编译器不会合成移动构造函数，这意味着 该类将 拥有有拷贝构造函数，但没有移动构造函数。
&emsp;&emsp; 如果一个类没有移动构造函数，函数匹配规则保证该类型的对象会被拷贝，即使我们试图通过调用`std::move()`也是如此：
```cpp
class Foo {
public:
    Foo() = default;
    Foo(const Foo&); // 显示定义 拷贝构造函数
    // 其它成员的定义, 但就是 没有定义 移动构造函数
};

Foo x;
Foo y(x);               // 拷贝构造函数; x 是一个左值
Foo z(std::move(x));    // 拷贝构造函数, 虽然 std::move(x) 返回的是右值，但Foo没有移动构造函数
```
#### 17.13.1 `Foo z(std::move(x))`为什么调用的是 拷贝构造函数？
&emsp;&emsp; 首先，`Foo`类没有自己的 移动构造函数，而且他定义了拷贝构造函数，因此编译器不会为其 合成移动构造函数，这意味着 该类将 拥有有拷贝构造函数，但没有移动构造函数。
&emsp;&emsp; 第二，我们知道`std::move(x)` 返回的是 一个绑定到`x`的`Foo &&`，而且我们可以将一个`Foo &&`转换为一个 `const Foo &`，因此根据函数的形参匹配规则，为 `Foo z(std::move(x))`调用 拷贝构造函数也是正确的。
&emsp;&emsp; 因此虽然编译器不能为`std::move(x)`精确匹配到移动构造函数，但是可以通过 对`std::move(x)`进行隐式转换为左值引用后 调用 拷贝构造函数。
#### 17.13.2 总结
&emsp;&emsp; 如果一个类有一个可用的拷贝构造函数而没有移动构造函数，则其对象是通过拷贝构造函数来“移动”的，拷贝赋值运算符和移动赋值运算符的情况类似。

#### 17.14 如何用单一的赋值运算符 同时实现 拷贝赋值运算符、移动赋值运算符 两个功能？
就拿前面写的 类值版本的`HasPtr` 来举例吧，下面的类的声明：
```cpp
class HasPtr {
public:
    // 新增了 移动构造函数
    HasPtr(HasPtr &&p) noexcept : ps(p.ps), i(p.i) {p.ps = 0;}

    // 下面这个 赋值运算符  既是拷贝赋值运算符，又是移动赋值运算符
    HasPtr& operator=(HasPtr rhs)
        { swap(*this, rhs); return *this; }

    // other members as in § 13.2.1 (p. 511)
};
```
**书上的描述：**
&emsp;&emsp; 我们来看一下上面的 赋值运算符：它有一个非引用参数，这意味着此参数要进行拷贝初始化。依赖于实参的额类型，拷贝初始化要么使用拷贝构造函数，要么使用移动构造函数----左值被拷贝，右值被移动。因此，单一赋值运算符就实现了拷贝赋值运算符和移动运算符两种功能。
**解释一下书上的话**
我先来看下面的例子：
```cpp
HasPtr hp, hp2;
hp = hp2;	            //因为p2 是一个左值，因此hp2将通过拷贝构造函数来拷贝
hp = std::move(hp2);	//std::move(hp2)返回一个绑定了hp2的右值引用，所以通过 移动构造函数 来移动hp2
```
**在上面的代码中有两次赋值**：
> **第一次赋值**：`hp = hp2`
>&emsp;&emsp; 因为`hp2`的类型是`HasPtr`，也就是说`hp2`是一个左值，因此移动版本的赋值运算符是不可行的，因为我们不能隐式地将一个右值绑定到一个左值，因此这里使用的是 拷贝赋值运算符。
> **第二次赋值**：`hp = std::move(hp2)`
>&emsp;&emsp; 在这个赋值中，`std::move(hp2)`的返回值是一个右值。在这个赋值中，拷贝赋值运算符 和 移动赋值运算符 都可以用，因为 我们可以将隐式的将一个右值绑定到左值上。 但调用拷贝赋值运算符 需要进行一次`const`的转换，而`HasPtr&&`则是精确匹配，因此这里会使用 移动赋值运算符。
>
因此，在上述的`HasPtr`类的对象中，不管使用的是拷贝构造函数还是移动构造函数，赋值运算符的函数体都会 swap 两个运算对象的状态。

### 17.15 更新三五法则
&emsp;&emsp; 所有五个拷贝成员应该看成一个整体：一般来说，如果一个类定义了任何一个拷贝操作，它就应该定义所有五个操作。

### 17.16 为`Message`类定义移动操作
```cpp
void Message::move_Folders(Message * m)
{
    folders = std::move(m->folders); // 使用set容器的移动赋值运算符
    for(auto f : folders){
        f->remMsg(m);     // 从Folder中删除旧的Message
        f->addMsg(this);  // 将本Message加到Folder中
    }
    m->folders.clear(); // 确保被移动后的 旧Message的folders是无害的
}

Message::Message(Message &&rhs):contents(std::move(rhs.contents)) // folders成员将执行 默认初始化
{
    move_Folders(&rhs);
}

Message& Message::operator=(Message &&rhs)
{
    if(this != &rhs){ // 直接检查是否为自赋值
        remove_from_Folders();  // 从所有包含自己的Folder 删掉自己
        contents = std::move(rhs.contents);
        move_Folders(&rhs);
    }
    return *this
}
```

### 17.17 使用移动操作时需要注意什么？
&emsp;&emsp; 由于一个移后源对象具有不确定状态，对其调用 std::move 是危险的。当我们调用move 时，必须绝对确认移动后源对象没有其它用户。
&emsp;&emsp; 通过在代码中小心的使用`move()`可以大幅度提高性能，而如果随意在普通代码中使用移动操作，很可能导致莫名其妙、难以查找的错误，起到相反的效果。






&emsp;
&emsp;
## 18 移动迭代器
### 18.1 什么是移动迭代器？
&emsp;&emsp; 移动迭代器(move iterator) 是在C++11中引入的一个特性，一个移动迭代器 通过改变给定迭代器的解引用运算符的行为 来适配此迭代器。
&emsp;&emsp; 一般来说一个迭代器的解引用运算符返回一个指向元素的左值，与其他迭代器不同，移动迭代器的解引用运算符将生成一个右值引用。

### 18.2 移动迭代器 的作用是？
&emsp;&emsp; 移动迭代器 的解引用返回一个 右值引用，在需要右值引用的情形下使用。

### 18.3 移动迭代器 和 普通的迭代器有何区别？
它们的区别在于 解引用后的返回值：
&emsp;&emsp; **普通的迭代器** 的解引用运算符 返回一个指向元素的左值
&emsp;&emsp; **移动迭代器** 的解引用运算符 返回一个右值引用

### 18.4 怎么获取 移动迭代器？
&emsp;&emsp;通过标准库函数`make_move_iterator()` 可以将 一个普通的迭代器 转换为 一个移动迭代器，此函数接收一个迭代器参数，返回一个 移动迭代器。

### 18.5 用移动迭代器重写 `StrVec::reallocate()`函数
&emsp;&emsp; 在之前版本的 `StrVec::reallocate()`中，我们使用一个for循环来调用`construct`来将元素拷贝到新内存：
```cpp
while(elem != first_free)
    alloc.construct(dest++ ,std::move(*elem++));
```
作为一种替换的方法，如果我们可以调用`uninitialized_copy()`来构造新分配的内存，将比循环更为简单，但问题是`uninitialized_copy()`恰如其名：他对元素进行拷贝，而不是移动，而且标准库也没有类似的函数 可以将对象“移动”到未构造的内存中，在个时候，移动迭代器就派上用场了：
```cpp
void StrVec::reallocate()
{
    auto newcapacity = size() ? 2*size() : 1;
    auto first = alloc.allocate(newcapacity);
    auto last = uninitialized_copy(make_move_iterator(begin()), make_move_iterator(end()))
    free()
    elements = first;
    first_free = last;
    cap = elements + newcapacity;
}

    size_t new_capacity = size() ? 2*size() : 1;
    auto new_data = alloc.allocate(new_capacity);
    auto dest = new_data;
    auto elem = elements;
    while(elem != first_free)
        alloc.construct(dest++ ,std::move(*elem++));
    free();
    elements = new_data
    first_free = dest
    cap = new_data + new_capacity;       
}
```

### 18.6 使用移动迭代器时 需要注意什么？
&emsp;&emsp; 标准库不保证哪些算法适用于 移动迭代器，哪些不适用。由于移动一个对象可能销毁掉原对象，因此只有在确信算法在为一个元素赋值或将其传递给一个用户定义的函数后不再访问它时，才能将移动迭代器传递给算法。






&emsp;
&emsp;
## 19 右值引用 和 成员函数
&emsp;&emsp; 除了构造函数和赋值运算符外，如果成员函数同时提供拷贝和移动版本，他也能从中受益。
### 19.1 成员函数 的移动版本 的工作原理是：
&emsp;&emsp; 函数重载。
&emsp;&emsp; 这种允许移动的成员函数通常使用和拷贝/移动构造函数 和 拷贝/移动运算符相同的参数模式：一个版本接收指向const的左值引用，第二个版本接受一个指向非const的右值引用。
&emsp;&emsp; 例如，定义有`push_back()`的标准库容器就提供了两个版本：一个接收指向const的左值引用，另一个接受一个指向非const的右值引用：
```cpp
void push_back(const X&);	// 拷贝，绑定到任意类型的X
void push_back(X&&);	    // 移动，只能绑定到类型X的可修改右值
```
当传递给`push_back`是一个 右值引用的时候，能与`void push_back(X&&)`精确匹配，编译器会选择运行 指向非const的右值引用版本的`push_back()`函数

### 19.2 为`StrVec`类定义一个 移动版本的`push_back()`函数
```cpp
class StrVec {
public:
    void push_back(const std::string&); // 拷贝元素
    void push_back(std::string&&);      // 移动元素
    // 其它元素如前
};

// 和之前的一样
void StrVec::push_back(const string& s)
{
    chk_n_alloc(); // 确保有足够的空间
    // construct a copy of s in the element to which first_free points
    alloc.construct(first_free++, s);
}

// 移动版本的 push_back()
void StrVec::push_back(string &&s) // 形参类型是 右值
{
    chk_n_alloc(); // 确保有足够的空间
    alloc.construct(first_free++, std::move(s));
}
```
当我们调用`push_back`时，实参类型决定了新元素是拷贝还是移动到容器中：
```cpp
StrVec vec; // empty StrVec
string s = "some string or another";
vec.push_back(s);       // 调用 push_back(const string&)
vec.push_back("done");  // 调用 push_back(string&&)
```
#### 19.2.1 为什么 `vec.push_back("done")` 调用的是 移动版本的 `push_back()`？
&emsp;&emsp; "done"是一个字符型字面常量，也就是说它不是一个左值，而是一个右值，左值是那些可以通过地址符`&`获取地址的变量，而"done"不能获得地址，因此它是一个右值。

### 19.3 能否向右值 赋值？
可以的：
```cpp
string s1 = "a value", s2 = "another";
auto n = (s1 + s2).find('a');
s1 + s2 = "wow!";   // s1 + s2 是右值，这里向右值赋值了
```

### 19.4 引用限定符
#### 19.4.1 引用限定符的作用是？
&emsp;&emsp; 引用限定符 用于成员函数的定义，**它用来“限定” 成员函数的`this指针`指向的对象的值的属性**：
> 对于`&` 限定的函数，`this指针`指向的对象 只能是 左值；
> 对于`&&`限定的函数，`this指针`指向的对象 只能是 右值
> 
#### 19.4.2 如何使用 引用限定符？
&emsp;&emsp; 我们指出 this的 左值/右值属性 的方式和 定义const成员函数 相同，即，在参数列表后面 放置一个 引用限定符：
#### 19.4.3 引用限定符 "限定"的是谁？
&emsp;&emsp; 引用限定符 “限定”的是 `this指针`。也是因为这个原因，引用限定符才只能用于 成员函数，因为只有类才有`this指针`。因此对于 赋值运算符来说，引用限定符限定的是它左侧的对象，因为拷贝赋值运算符 的`this指针`是绑定到 `=`左侧的对象的。

#### 19.4.4 使用 引用限定符的时候需要注意什么？
&emsp;&emsp; 引用限定符只能用于(非static)成员函数，而且必须同时出现在函数的声明和定义中

#### 19.4.5 const限定符和 引用限定符同时出现时 对它俩的顺序有要求吗？
&emsp;&emsp; 一个函数可以同时使用 const 和引用限定符，在此情况下，引用限定符必须跟随在const 限定符之后：
```cpp
class Foo{
public:
	Foo someMes() & const; 	//错误，const限定符必须在前
	Foo someMes() const &; 	//正确，const限定符必须在前
};
```

### 19.5 如何在自己的类中阻止向一个右值(左值)赋值？
&emsp;在C++11之前，我们没有办法阻止 向右值 赋值，在C++11中我们可以前置 左侧运算对象(也就是this指针指向的对象)是一个左值：我们指出 this的 左值/右值属性 的方式和 定义const成员函数 相同，即，在参数列表后面 放置一个 引用限定符：
> &emsp;&emsp; 引用限定符 可以是`&` 或 `&&`，类似于 const限定符，引用限定符只能用于(非static)成员函数，因为 引用限定符 “限定”的是 `this指针`，而只有类才有`this指针`，因此只能用于 成员函数。
> &emsp;&emsp; 引用限定符 必须同时出现在函数的 声明 和 定义 中。
> &emsp;&emsp; 对于`&`限定的函数，我们只能将它用于左值；、对于 `&&`限定的函数，我们只能将它用于右值。
> 

### 19.6 写一个类`Foo`，它阻止向一个 `Foo`类型的右值 赋值
(1) 因为阻止的是向右值赋值，所以应该使用 引用限定符`&`
(2) 因为是阻止赋值，所以应该在 类`Foo`的拷贝赋值运算符的声明和定义中使用 引用限定符`&`
```cpp
class Foo {
public:
Foo &operator=(const Foo&) &; // 加了引用限定符，因此只能向可修改的左值赋值
    // other members of Foo
};

Foo &Foo::operator=(const Foo &rhs) &
{
    // 执行将rhs赋予本对象(this)所需的工作
    return *this;
}

Foo &retFoo();  // retFo()函数返回一个引用，而调用一个 返引用的函数 将得到左值（第六章有讲）
Foo retVal();   // retVal()函数返回的是一个值，这意味它返回的是 右值
Foo i, j;       // i 和 j 是左值
i = j;          // 正确: i 是左值
retFoo() = j;   // 正确: retFoo()返回一个引用，这意味着调用它将返回左值
retVal() = j;   // 错误: retVal() 返回的是右值，我们不能向右值赋值
i = retVal();   // 正确: w我们可以将右值作为赋值运算符的右侧运算符
```
#### &emsp;19.6.1 `retVal() = j;`为什么是错误的？
&emsp;&emsp; 我们先来看一下`Foo`类的 拷贝赋值运算符 的声明:
```cpp
Foo &operator=(const Foo&) &; 
```
我们一步一步来解释一下：
> &emsp;&emsp;① 可以看到，`Foo`类的 拷贝赋值运算符 里有 引用限定符`&`，这意味着 `Foo`类的 拷贝赋值运算符 只能用于左值；
> &emsp;&emsp;② 另外，引用限定符 “限定”的是 `this指针`，而 拷贝赋值运算符 的`this指针`是绑定到 `=`左侧的对象的，也就是说拷贝赋值运算符左侧的对象(即`=`左侧对象)必须是一个左值；
> &emsp;&emsp;③ 我们来看`retVal() = j;`，在这个语句中， `=`左侧的对象是`retVal()`函数的返回值，但问题是 `retVal()`函数返回的是一个右值，因此他违反了 `Foo`类的拷贝赋值运算符能用于左值 的限定，所以它不正确。
> 
#### &emsp;19.6.2 `retFoo() = j;`为什么正确？
&emsp;&emsp; `retFoo()`函数返回的就是一个引用，而调用一个 返引用的函数 将得到一个左值，所以它正确。

### 19.7 引用限定符 在函数重载的时候 起作用吗？
&emsp;&emsp; 就像成员函数可以根据是否有const来区分重载版本，引用限定符也可以区分重载版本，并且还可以 综合引用限定符和const 来区分一个成员函数的重载版本：
在下面的代码中，我们将为`Foo`定义一个名为data的vector成员和一个名为sorted的成员函数，sorted返回一个Foo对象的副本，该副本中data成员已被排序：
```cpp
class Foo{
public:
	Foo sorted() &&;	    // 可用于改变的右值
	Foo sorted() const &;	// 可用于任何类型的Foo
private:
	vector<int> data;
};

//本对象是右值，因此可以原址排序
Foo Foo::sorted() &&
{
	sort(data.begin(),data.end());
	return *this;
}

//本对象是const或是一个左值，哪种情况我们都不能对其进行原址排序
Foo Foo::sorted() const &
{
	Foo ret(*this);			//拷贝一个副本
	sort(ret.begin(),ret.end());	//排序副本
	return ret;	//返回副本
}
```
&emsp;&emsp; 当我们对一个右值执行sorted时，他可以安全的直接对data成员进行排序，因为对象是一个右值就意味着没有其他用户，因此我们可以改变对象。
&emsp;&emsp; 当对 一个const右值 或 一个左值执行sorted时，我们不能改变对象，因此就需要在排序前拷贝data。
&emsp;&emsp; 在调用sorted时，编译器会根据sorted的对象的 左值/右值属性来确定使用哪个sorted版本：
```cpp
Foo &retFoo();  // retFo()函数返回一个引用，而调用一个 返引用的函数 将得到左值（第六章有讲）
Foo retVal();   // retVal()函数返回的是一个值，这意味它返回的是 右值

retFoo.sorted();	// retFoo()是一个左值，调用Foo::sorted() const &
retVal.sorted();	// retVal()是一个右值，调用Foo::sorted() &&
```

### 19.8 定义 引用限定符 函数时需要注意什么？
&emsp;&emsp; 当我们定义 const成员函数时，可以定义两个版本，唯一的差别是 一个版本有const限定，另一个没有：
```cpp
class Demo
{
    Foo test() const;
    Foo test();
};
```
而引用限定函数则不一样：如果我们定义两个或者两个以上 具有相同名字和相同参数列表 的成员函数，就必须对所有函数都加上引用限定符，或者所有都不加（注意，是函数名字和参数列表都相同！）：
```cpp
class Foo
{
	Foo sorted() &&;
	Foo sorted() const;	//错误，函数名字和参数列表都相同，所以必须加上引用限定符
	using Comp = bool (const int&,const int&)；
	Foo sorted(Comp*);	        //正确，虽然同名，但有着不同的参数列表
	Foo sorted(Comp*) const;	//正确，两个版本都没有使用引用限定符
};
```
&emsp;&emsp; 在上面的代码中，声明了一个 没有参数的const版本的sorted，此声明是错误的，因为Foo类中还有一个无参数版的sorted版本，它有一个引用限定符，因此const版本也必须有引用限定符。
&emsp;&emsp; 另一方面，接收一个比较操作指针的sorted版本是没有问题的，因为两个函数都没有 引用限定符。
正确的定义如下：
```cpp
class Foo
{
	Foo sorted() &&;
	Foo sorted() const &;	//正确
	using Comp = bool (const int&,const int&)；
	Foo sorted(Comp*);	
	Foo sorted(Comp*) const;	
};
```
**注意** ：如果一个成员函数有引用限定符，则具有相同参数列表的所有版本都必须具有引用限定符（仔细看，是函数名字和参数列表都相同！）。






&emsp;
&emsp;
## 20. 阅读网上关于右值引用的博文后的总结
### 20.1 
.TODO: https://zhuanlan.zhihu.com/p/107445960