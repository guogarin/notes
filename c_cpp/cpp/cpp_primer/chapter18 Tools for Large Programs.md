# 综述：用于大型程序的工具
&emsp;&emsp; 与仅需几个程序员就能开发完成的系统相比，大规模编程对程序设计语言的要求更高。大规模应用程序的特殊要求包括：
> ① 在独立开发的子系统之间协同处理错误的能力；
> ② 使用各种库（可能包含独立开发的库）进行协同开发的能力；
> ③ 对比较复杂的应用概念建模的能力。
> 
本章需要介绍的三种C++语言特性正好能满足上述要求：
> ① 异常处理
> ② 命名空间
> ③ 多重继承
> 







&emsp;
&emsp;
&emsp; 
# 一、异常处理(exception handling)







&emsp;
&emsp;
&emsp; 
# 二、命名空间(namespace)
## 1. 为什么需要 命名空间？
### 1.1 待解决的问题
&emsp;&emsp; 大型程序往往会使用多个独立开发的库，这些库又会定义大量的全局对象（如类、函数、模板等）。当应用程序用到多个供应商提供的库时，不可避免的会发生某些名字冲突的情况，此时如果将这些库的名字放置在全局命名空间中将引发**命名空间污染(namespace pollution)**。
### 1.2 传统的解决方法
&emsp;&emsp; 传统上，程序员通过将其定义全局名字设置的很长，以此来避免命名空间污染问题，这样的名字通常包含名字所属库的前缀部分：
```cpp
// cplusplus 前缀
class cplusplus_primer_Query { ... };
string cplusplus_primer_make_plural(size_t, string&);
```
这种解决方案显然不太理想：
> 对于程序员来说，书写、阅读 这么长的名字费时费力且过于繁琐。
> 
### 1.3 更优秀的解决方法：命名空间
&emsp;&emsp; 命名空间(namespace) 为防止名字冲突提供了更加可控的机制：
> 命名空间分割了全局命名空间，其中每个命名空间都是一个作用域，通过在某个命名空间中定义库的名字，可以避免命名冲突。
> 
### 1.3 总结
&emsp;&emsp; 命名空间其实就是用来解决命名冲突的。

&emsp; 
## 2. 命名空间的定义
### 2.1 如何定义一个命名空间？
&emsp;&emsp; 一个命名空间的定义包含两部分：
> ① 关键字`namespace`
> ② 命名空间的名字，并在名字的后面跟着一系列由花括号括起来的声明和定义。
> 
只要能出现在全局作用域中的声明就能置于命名空间内：
```cpp
namespace cplusplus_primer {
    class Sales_data { / * ... * /};
    Sales_data operator+(const Sales_data&,
                          const Sales_data&);
    class Query { /* ... */ };
    class Query_base { /* ... */};
} // 注意，无需分号
```
上面的代码定义了一个名为`cplusplus_primer`的命名空间，该命名空间包含四个成员：3个类 和 一个重载的`+`运算符。

&emsp;
## 3. 为什么命名空间可以解决命名冲突？
&emsp;&emsp; 因为每个命名空间都是一个单独的作用域，所以在不同的命名空间内可以有相同的名字。

&emsp;
## 4. 如何访问一个命名空间内的成员？
&emsp;&emsp; 定义在某个命名空间中的名字可以被该命名空间内的其它成员直接访问，也可以被这些成员内嵌的任何单位访问：
```cpp
namespace cplusplus_primer {
    class Query { /* ... */ };

    Query q = Query("hello"); // 直接访问同一命名空间内的Sales_data类
} 
```
&emsp;&emsp; 如果需要在该命名空间之外，则需要明确指出所用的名字属于哪个命名空间：
```cpp
// 需明确指出命名空间
cplusplus_primer::Query q =  cplusplus_primer::Query("hello"); 

AddisonWesley::Query q = AddisonWesley::Query("hello");
```

&emsp;
## 5. 命名空间定义的连续性
### 5.1 命名空间是否可以不连续？
&emsp;&emsp; 这其它作用域不太一样的是，命名空间可以是不连续的，它可以定义的在几个不同的部分：
```cpp
#include <iostream>

using namespace std;

namespace Discontiguous{
    void print_hello(){
        cout << "Hello world!" << endl;
    }
}


namespace Discontiguous{
    void print_greeting(){
        cout << "How are you doing?" << endl;
    }
}


int main()
{
    Discontiguous::print_hello();
    Discontiguous::print_greeting();
}
```
运行结果：
```
Hello world!
How are you doing?
```
### 5.2 这个特性有何作用？
&emsp;&emsp; 命名空间的定义可以不连续的特性 使得我们可以将几个独立的接口和实现文件组成一个命名空间，此时，命名空间的组织方式类似于我们管理自定义类及函数的方式：
> ① 命名空间的一部分成员的作用是定义类、**声明**作为类接口函数及对象，这部分成员应该位于头文件中；
> ② 命名空间成员的**定义部分**则位于其它源文件中。
> 
**总结一下就是：** 
> 类的定义、函数的声明放在头文件中；类成员的定义、函数的定义都放在源文件中；
> 
这么做的目的和之前一样，都是为了做到 接口和实现分离。
&emsp;&emsp; 通过这个特性，我们可以将`cplusplus_primer`库定义在几个不同的文件中。
`Sales_data`的类声明及其函数放在`Sales_data.h`中：
```cpp
// ---- Sales_data.h----
// #includes should appear before opening the namespace
#include <string>

namespace cplusplus_primer {
    class Sales_data { /* ... */};
    Sales_data operator+(const Sales_data&,
                          const Sales_data&);
    // 其它接口的声明
}
```
实现文件放在`Sales_data.cc`文件中：
```cpp
// ---- Sales_data.cc----
// be sure any #includes appear before opening the namespace
#include "Sales_data.h"

namespace cplusplus_primer {
    // Sales_data成员及重载运算符的定义
}
```
如果程序想使用`cplusplus_primer`库，则需要包含这些该文件：
```cpp
// ---- user.cc----
// names in the Sales_data.h header are in the cplusplus_primer namespace
#include "Sales_data.h"

int main()
{
    using cplusplus_primer::Sales_data;
    Sales_data trans1, trans2;
    // ...
    return 0;
}
```

&emsp;
## 6. 全局命名空间(global namespace)
### 6.1 什么是 全局命名空间？
&emsp;&emsp; 在全局作用域中定义的名字 也就是定义在了全局命名空间中：
```cpp
#include<iostream>

using namespace std;

int num = 100; // num变量 是一个定义在全局作用域的名字，换句话说，它被定义在了全局命名空间中。
```
### 6.2 如何访问全局命名空间中的成员？
&emsp;&emsp; 作用域运算符同样可以用于全局作用域的成员，因为全局作用域是隐式的，所以它没有名字，下面是它的访问形式：
```cpp
::member_name
```

&emsp;
## 7. 嵌套的命名空间(Nested Namespaces)
&emsp;&emsp; 嵌套的命名空间是指 定义在其它命名空间中的命名空间，和过往的规则一样，内层命名空间的声明将隐藏外层命名空间声明的同名成员：
```cpp
#include <iostream>

using namespace std;

namespace nested{
    int num = 0;
    namespace inner_1{
        int num = 50;
    }

    namespace inner_2{
        int num = 100;
    }
}

int main()
{
    cout << "nested::num          : " << nested::num << endl;
    cout << "nested::inner_1::num : " << nested::inner_1::num << endl;
    cout << "nested::inner_2::num : " << nested::inner_2::num << endl;
}
```
运行结果：
```
nested::num          : 0
nested::inner_1::num : 50
nested::inner_2::num : 100
```

&emsp;
## 8. 内联命名空间(inline namespace)
&emsp;&emsp; 内联命名空间是C++11引入的一种新的嵌套命名空间，和普通嵌套命名空间不一样的是，内联命名空间中的名字可以被外层命名空间直接使用，也就是说，我们可以无需在内联命名空间的名字前添加表示该命名空间的前缀，通过外层命名空间的名字就可以直接访问它：
&emsp;&emsp; 定义内联命名空间的方式就是在关键字`namespace`前添加关键字`inline`：
```cpp
#include <iostream>
using namespace std;

namespace A {
    inline namespace inlineA1 {
       void funcA1() {
           cout << "inlineA1()" << endl;
       }
    }
    inline namespace inlineA2 {
        void funcA2() {
            cout << "inlineA2()" << endl;
        }
    }
}
int main() {
    // A::inlineA1::funcA1();  //不需要指定inlineA1
    A::funcA2();  //inlineA2()
    return 0;
}
```
运行结果：
```
inlineA2()
```

&emsp;
## 9. 未命名的命名空间
### 9.1 什么是 未命名的命名空间？
&emsp;&emsp; 未命名的命名空间(unnamed namespace)是指 关键字`namespace`后紧跟花括号括起来的一系列声明语句：
```cpp
namespace { // 注意，namespace后面没有名字
    class Sales_data { /* ... */};
}
```

### 9.2 未命名的命名空间中定义的变量 有什么特点？
&emsp;&emsp; 未命名的命名空间中定义的变量具有静态生命周期：它们在第一次使用前被创建，直到程序结束时才销毁。

### 9.3 未命名的命名空间中定义的变量 的可见性是怎样的？
&emsp;&emsp; 和其他命名空间不同，未命名的命名空间仅在特定的文件内部有效，其作用范围 **不会** 横跨多个不同的文件。

### 9.4 如果两个文件中都含有未命名的命名空间，它们会冲突吗？
&emsp;&emsp; 不会，每个文件都可以定义自己的未命名的命名空间，如果两个文件都含有未命名的命名空间，则这两个空间互相无关。在这两个未命名的命名空间里面可以定义相同的名字，并且这些定义表示的是不同实体。如果一个头文件定义了未命名的命名空间，则该命名空间中定义的名字将在每个包含了该头文件的文件中对应不同实体。

### 9.5 如何使用 未命名的命名空间中定义的对象？
&emsp;&emsp; 定义在未命名的命名空间中的名字可以直接使用，毕竟我们找不到什么命名空间的名字来限定它，同样的我们也不能对 未命名的命名空间的成员使用作用域运算符：
```cpp
#include <iostream>

using namespace std;

namespace {
    int num = 100;
}

int main()
{
    cout << "num : " << num << endl;
}
```
运行结果：
```
num : 100
```
**结果分析：**
&emsp;&emsp; 可以看到的是，在外面直接使用就行。

### 9.6 如果在全局作用域和未命名的命名空间中 定义了同名对象，会发生什么？
&emsp;&emsp; 会导致二义性：
```cpp
#include <iostream>

using namespace std;

int num = 0;

namespace {
    int num = 100;
}

int main()
{
    cout << "num : " << num << endl;
}
```
运行结果：
```
test.cpp: 在函数‘int main()’中:
test.cpp:13:25: 错误：对‘num’的引用有歧义
     cout << "num : " << num << endl;
                         ^
test.cpp:5:5: 附注：备选为： int num
 int num = 0;
     ^
test.cpp:8:9: 附注：         int {匿名}::num
     int num = 100;
         ^
```
**结果分析：**
&emsp;&emsp; 显然，这么写编译时就报错。

### 9.7 在实际中，未命名的命名空间 的作用是？
&emsp; 未命名的命名空间 被用来 取代文件中的静态声明：
> &emsp;&emsp; 在标准C++引入命名空间的概念之前，程序需要将名字声明成static的以使其对于整个文件有效。在文件中进行静态声明的做法是从C语言继承而来的。在C语言中，声明为static的全局实体在其所在的文件外不可见。
> 
在文件中进行静态声明的做法已经被C++标准取消了，现在的做法是使用未命名的命名空间。
定义静态变量的传统做法：
```cpp
static int num;
```
现在定义静态变量的做法：
```cpp
namespace {
    int num;
}
```

&emsp;
## 10. 使用命名空间成员
### 10.1 有哪些方法使用命名空间成员？
**(1) 命名空间别名(Namespace Aliases)**
命名空间别名 使得我们可以为命名空间的名字设定一个短得多的同义词：
```cpp
namespace foo = tomocat_foo;
// 命名空间的别名也可以指向一个嵌套的命名空间
namespace foo = tomocat::tomocat_foo;
```
**(2) using声明**
* 有效范围从using声明的地方开始，一直到using声明所在的作用域结束为止
* 未加限定的名字只能在using声明所在的作用域以及内层作用域中使用
* 一条using声明可以出现在全局作用域、局部作用域、命名空间作用域以及类的作用域中；在类的作用域中，这样的声明语句只能指向基类成员（因为派生类只能为那些它可以访问的名字提供using声明）
```cpp
using std::endl;
using std::cout;
```
**(3) using指示**
&emsp; `using`指示会引入指定命名空间内所有成员的名字，它以关键字`using`开始，后面是关键字`namespace`以及命名空间的名字，例如：
```cpp
using namespace std;
```
但是，在实际编程中禁止使用`using`指示，因为它会引入整个命名空间的标识符号从而污染命名空间。

### 10.2 using指示
#### 10.2.1 在实际应用中，为什么不建议使用`using`指示？
&emsp; 除了`using`指示都可以，因为`using`指示会一次性注入某个命名空间中的所有名字，这种用法充满风险：
> 命名空间中所有的成员变得可见了。
> 
因此，相比于使用`using`指示，在程序中对命名空间的每个成员分别使用`using`声明效果更好，这样可以减少注入到命名空间中的名字数量。
#### 10.2.2 using指示有什么正面作用吗？
&emsp; 当然，`using`指示也并非一无是处，例如在命名空间本身的实现文件中就可以使用。

&emsp; 
## 11. 实参相关的查找与类类型形参
<div align="center"> <img src="./pic/chapter18/实参相关的朝招与类类型形参.png"> </div>


&emsp; 
## 12. 查找`std::move`和`std::forward`
<div align="center"> <img src="./pic/chapter18/move和forward.png"> </div>

&emsp; 
## 13. 友元声明和实参相关的查找
<div align="center"> <img src="./pic/chapter18/友元声明和实参相关的查找.png"> </div>

&emsp; 
## 14. 重载与using声明
### 14.1 `using`声明引入的是什么？
using声明语句声明的是一个名字，而非一个特定的函数：
```cpp
using NS::print(int);   // 错误: 不能指定形参列表
using NS::print;        // 正确: using声明只声明一个名字
```
我们为函数书写`using`声明时，该函数的所有版本都被引入到当前作用域中。

&emsp; 
## 15.1 重载与using指示
&emsp;&emsp; `using`指示将命名空间的成员 提升到 外层作用中，如果命名空间中的某个函数 和 该命名空间所属作用域的函数同名，则命名空间的函数将被添加到重载集合中：
```cpp
namespace libs_R_us {
    extern void print(int);
    extern void print(double);
}
// ordinary declaration
void print(const std::string &);
// this using directive adds names to the candidate set for calls to print:、

using namespace libs_R_us;
// the candidates for calls to print at this point in the program are:
//    ① print(int) from libs_R_us
//    ② print(double) from libs_R_us
//    ③ print(const std::string &) declared explicitly

void fooBar(int ival)
{
    print("Value: "); // calls global print(const string &)
    print(ival); // calls libs_R_us::print(int)
}
```
如果存在多个`using`指示，则来自每个命名空间的名字都会成为候选函数的一部分：
```cpp
namespace AW {
    int print(int);
}

namespace Primer {
    double print(double);
}
​
// using指示从不同的命名空间中创建了一个重载函数集合
using namespace AW;
using namespace Primer;

long double print(long double);
int main() {
    print(1);   // 调用AW::print(int)
    print(3.1); // 调用Primer::print(double)
    return 0;
}
```
在全局作用域中，函数`print`的重载集合包括`print(int)`, `print(double)`, 以及 `print(long double)`，尽管它们来自不同的作用域，但是它们都属于`main`函数中`print`调用的候选函数集。

&emsp;
## 是否可以在命名空间内`include`头文件？
TODO:

&emsp;
## 命名空间和类有什么区别？
&emsp;&emsp; 首先，作用不一样：对C++来讲， 基本建模工具是类而不是命令空间，命令空间主要用来避免名字冲突
&emsp;&emsp; 第二，在使用方式上也不一样：命名空间`namespace`可以被再次打开，并添加新成员。但是类`class`不允许。举个简单的例子：
命名空间这么使用是正确的：
```cpp
namespace A {
    int f1();
}

// Re-open and add new member is legal
namespace A {
    int f2();
}
```
但是，类这么使用是不正确的：
```cpp
class A {
    int f1();
};

// Re-open and add new member is illegal
class A {
    int f2();
};
```

&emsp;
## 可以在`miain`函数内定义命名空间吗？
不可以，如果在`miain`函数内定义命名空间，编译时就会报错：
```cpp
#include <iostream>

int main()
{
    namespace test{
        int num = 100;
    }
}
```
运行结果：
```
test.cpp: 在函数‘int main()’中:
test.cpp:5:5: 错误：在这里不允许使用‘namespace’定义
     namespace test{
     ^
```






&emsp;
&emsp;
&emsp; 
# 三、多重继承和虚继承
## 1. 