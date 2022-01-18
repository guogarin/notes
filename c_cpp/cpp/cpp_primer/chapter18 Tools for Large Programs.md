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
### 9.2 

&emsp;
## 是否可以在命名空间内`include`头文件？


&emsp;
## 可以在`miain`函数内定义命名空间吗？