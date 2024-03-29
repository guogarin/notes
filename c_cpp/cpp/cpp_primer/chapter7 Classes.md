[toc]





&emsp;
&emsp;
# 七、类基础
&emsp;
&emsp;
## 1.	类的基本思想是？
&emsp;&emsp; 类的基本思想是 数据抽象（data abstraction）和封装（encapsulation）。






&emsp;
&emsp;
## 2.	数据抽象（data abstraction） 是什么？
数据抽象：一种依赖于 接口（interface）和实现（implementation）分离的编程技术。
&emsp;&emsp; **接口**：类的接口包括用户可以执行的操作；
&emsp;&emsp; **实现**：类的实现包括 类的数据成员、负责接口实现的函数体、定义类需要的各种私有函数






&emsp;
&emsp;
## 3.	封装（encapsulation）的作用是什么？
&emsp;&emsp; 封装实现了 类的接口 和 实现的分离，封装后的类隐藏了它的实现细节，也就是说，类的用户 只能使用接口，但无法看到类的实现






&emsp;
&emsp;
## 4.	什么是成员函数（member function）？
&emsp;&emsp; 成员函数是 定义为类的一部分 的函数，有时也称为 方法（method）。






&emsp;
&emsp;
## 5.	封装有什么好处
1 ) 确保用户代码不会无意间破坏封装对象的状态；
2 ) 被封装的类的具体实现细节可以随时改变 而不影响用户代码，因为用户使用的只是接口，只要接口不变，用户代码就无需改变；
3 ) 类改变后只需编译该类的源文件






&emsp;
&emsp;
## 6.	成员函数的 定义、声明的位置 有什么要求？
&emsp;&emsp; 成员函数必须在类的内部声明，但它的定义可以在类的内部，也可以在类的外部。






&emsp;
&emsp;
## 7.	非成员函数的定义、声明的位置 什么要求？
&emsp;&emsp; 非成员函数 一般也与类相关，但它们的定义和声明都在类的外部。






&emsp;
&emsp;
## 8.	成员函数 和 非成员函数的区别？
1 ) 声明的位置不同，成员函数声明在类的内部，而非成员函数声明在类的外部；
2 ) 调用方式不一样






&emsp;
&emsp;
## 9.	成员函数定义在类内还是类外有什么不同吗？
&emsp;&emsp; 成员函数 定义在类内部的话吗，那它将是隐式的inline函数。






&emsp;
&emsp;
## 10.	成员函数中，紧跟着参数列表的const关键字修饰的是？
&emsp;&emsp; 这说明该函数是常量成员函数，这个const修饰的是隐式this指针，因为this指针是隐式的（即不会出现的形参列表中），所以才把const写在了形参列表之后，用来说明this指针时 指向常量的指针（this指向的对象 是常量）。






&emsp;
&emsp;
## 11.	什么是this指针？
&emsp;&emsp; this指针 是 类的成员函数的 一个 隐式形参，当我们调用成员函数时，会用 请求该函数的对象的地址 初始化 this，后面就通过这个this隐式参数来访问调用它的那个对象，比如：
```cpp
Sales_item total;  
total.isbn()  
```
其实就相当于：
```cpp
//伪代码，用于说明调用成员函数的实际执行过程  
Sales_data::isbn(&total) // 把调用对象total的地址传进去，成员函数isbn()
```
在成员函数内部，我们可以直接使用调用该函数的对象的成员，而无须通过成员访问运算符来做到这一点，因为 this 所指的正是这个对象。任何对类成员的直接访问都被看作是对 this 的隐式引用，也就是说，当 isbn 使用 bookNo 时，它隐式地使用 this 指向的成员，就像我们书写了 this->bookNo 一样。
对于下面的代码，
```cpp
std::string isbn() const   
{   
    return bookNo;   // 在编译器看来，它相当于 this->bookNo，因为在成员函数的内
                       // 部，任何对类成员的直接访问都被看作是对 this 的隐式引用。
}  
```
其实相当于：
```cpp
std::string isbn() const   
{   
    return this->bookNo;   // 显式的使用了this指针，其实可以省略this。
}  
```






&emsp;
&emsp;
## 12.	this指针是什么类型？
&emsp;&emsp; 它是一个指针常量，是一个顶层const，即this指针自身是一个常量，因此我们不允许改变this指针中保存的地址。
&emsp;&emsp; 对于类型Sales_data类的成员函数来说， this指针 的类型是： `Sales_data *const` 






&emsp;
&emsp;
## 13. this指针 对 sizeof（类对象）的影响？
&emsp;&emsp; 一个对象的this指针并不是对象本身的一部分，不会影响sizeof(对象)的结果。






&emsp;
&emsp;
## 14. this指针到底是不是register 变量？
&emsp;&emsp; 看编译器，有的是，有的不是。






&emsp;
&emsp;
## 15. 友元是否有this指针？
&emsp;&emsp; 成员函数才有this指针，而友元并不是成员函数，因此它没有。






&emsp;
&emsp;
## 16.	对于`class A{public: int func(int p){}}`, 在编译器看来,func的原型应该是怎样的？
&emsp;&emsp; 在编译器看来，成员函数的第1个参数为this指针，所以func的原型应该是：
```cpp
int func(A* const this, int p); // 第一个参数应该是this指针
```






&emsp;
&emsp;
## 17.	this指针是什么时候创建的？
&emsp;&emsp; 对于`class A{public: int func(int p){}}`, 在编译器看来成员函数的第1个参数为this指针，所以func的原型应该是：
```cpp
int func(A* const register this, int p); // 第一个参数应该是this指针，注意register 关键字，这意味着this是放在寄存器里面的
```
由此可见，this在成员函数的开始前构造，在成员函数的结束后清除。
this指针的生命周期同任何一个函数的参数是一样的，没有任何区别。当调用一个类的成员函数时，编译器将类的指针作为函数的this参数传递进去。如：
```cpp
A a;  
a.func(10);  
// 此处，编译器将会编译成：  
A::func(&a, 10);  
```
&emsp;&emsp; 换句话说，this指针其实是成员函数的隐式形参，既然它是形参，那就意味着它是进入函数时创建，离开函数时销毁。（虽然this是隐式形参，但它有可能存放在栈中，也可能则寄存器中，看编译器！）






&emsp;
&emsp;
## 18.	如果我们知道一个对象this指针的位置，可以直接使用吗？
&emsp;&emsp; 不能，因为this指针其实是成员函数的隐式形参，既然它是形参，那就意味着它是进入函数时创建，离开函数时销毁，所以，我们无法知道一个对象的this指针的位置（只有在成员函数里才有this指针的位置）。当然，在成员函数里，你是可以知道this指针的位置的（可以通过&this获得），也可以直接使用它。
&emsp;&emsp; 因此，我们只有获得一个对象后，才能通过对象使用this指针。






&emsp;
&emsp;
## 19.	this指针存放在何处？堆、栈、全局变量，还是其他？
&emsp;&emsp; this指针会因编译器不同而有不同的放置位置。可能是栈，也可能是寄存器。






&emsp;
&emsp;
## 20.	在成员函数的声明中，形参列表后面还有一个const是什么意思？
&emsp;&emsp; 这表明这个函数时const成员函数（const member function）






&emsp;
&emsp;
## 21.	 常量成员函数有何作用？
1 ) 确保成员函数不会修改调用它的对象；
2 ) 如果不需要在成员函数中修改调用对象，那么声明为常量成员函数可以提高 接受参数的灵活性：
如果调用对象是一个const，那么它将不能调用非常量成员函数，this虽然是隐式的，但它还是得遵守初始化原则：不能用const对象来初始化 非cosnt对象，因此我们不能以常量对象来调用非常量成员函数，这相当于：
```cpp
//伪代码  
Sales_data * const this;  // this是顶层const  
const Sales_data total;   // total是底层const  
this = &total             // 错误，不能用const对象来初始化 非cosnt对象  
```






&emsp;
&emsp;
## 22.	对于下面的类，bookNo成员定义在isbn()后面，那isbn()能否使用bookNo成员呢？为什么？
```cpp
struct Sales_data {  
    // new members: operations on Sales_data objects  
    std::string isbn() const { return bookNo; }  
    Sales_data& combine(const Sales_data&);  
    double avg_price() const;  
    // data members are unchanged from § 2.6.1 (p. 72)  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
};  
// nonmember Sales_data interface functions  
Sales_data add(const Sales_data&, const Sales_data&);  
std::ostream &print(std::ostream&, const Sales_data&);  
std::istream &read(std::istream&, Sales_data&);  
```
虽然，bookNo成员定义在isbn()后，但是isbn()还是可以使用bookNo成员。
因为编译器分两步处理类：
	① 首先编译所有 类成员的声明；
	② 然后才编译 函数体；
因此后声明的函数还是可以声明在他前面声明的成员。






&emsp;
&emsp;
## 23.	在类外 定义成员函数需要注意什么？
① 函数名、形参列表 必须和类内匹配；
② 外部定义的成员 必须包含 它所属的类名（注意类名所在的位置）：
```cpp
// 注意类名所在的位置，它在 返回值 和 函数名 之间
double Sales_data::avg_price() const {  
if (units_sold)  
    return revenue/units_sold;  
else  
    return 0;  
}  
```






&emsp;
&emsp;
## 24.	如何设计一个返回 this对象 的函数？
函数的返回值的类型 必须和 return的对象 匹配：
```cpp
// 解引用this之后得到的是Sales_data对象，因此返回值类型应该是 Sales_data的引用
Sales_data& Sales_data::combine(const Sales_data &rhs)  
{  
    units_sold += rhs.units_sold; // add the members of rhs into  
    revenue += rhs.revenue; // the members of ''this'' object  
    return *this; 			 // 解引用后得到的是 Sales_data对象
}  
total.combine.(trans); // 更新变量total的当前值
```
1 ) 对于调用 `total.combine(trans)` ，它的绑定情况是怎样的？
trans绑定到rhs形参，total绑定到this指针。






&emsp;
&emsp;
## 25.	构造函数的作用是？
&emsp;&emsp; 构造函数（constructor）是类用来 控制其对象的初始化过程 的函数。






&emsp;
&emsp;
## 26.	构造函数有什么特点？
1 ) 构造函数的名字和类的名字相同；
2 ) 没有返回类型；
3 ) 一个类可以有多个构造函数，但是它们的实参列表必须不一样；
4 ) 不能声明为const（const成员函数）






&emsp;
&emsp;
## 27.	一个类可以没有构造函数吗？
&emsp;&emsp; 可以，如果类的设计者 没有定义 任何构造函数，那编译器会隐式的定义一个默认构造函数，这个隐式定义的函数也叫 合成的默认构造函数（synthesized default constructor）。






&emsp;
&emsp;
## 28.	谁来控制类的默认初始化？
&emsp;&emsp; 默认构造函数用来控制






&emsp;
&emsp;
## 29.	默认构造函数什么时候被调用
若定义一个对象时没有提供初始化式，就使用默认构造函数，比如：
```cpp
Sales_data total; // 没有提供初值，此时调用默认构造函数初始化
```






&emsp;
&emsp;
## 30.	默认构造函数有何特点？
一个函数是默认构造函数 当且仅当 调用它可以不需要传入任何参数，定义默认构造函数有两种方式，
&emsp;&emsp; ① 没有任何参数；
&emsp;&emsp; ② 定义的所有参数 都有默认值；
```cpp
class testClass  
{  
public:  
    testClass();                     // 默认构造函数  
    testClass(int a, char b);        // 构造函数  
    testClass(int a=10,char b='c');  // 所有参数 都有默认值，所以是默认构造函数 
  
private:  
    int  m_a;  
    char m_b;  
};  
```
注意，“定义的所有参数 都有默认值”并不是说 所有类内所有成员 都得有默认值，而是 构造函数中的所有形参都有默认值：
```cpp
class Message {  
public:  
    Message(const string &str =""): contents(c) { }// 注意，这是个默认构造函数！  
    Message();//构造函数  
private:  
    string contents;  
    set<Folder*>folders;  
};  
```
在上面的`Message类`中，它含有连个成员：contents和folders，但
```cpp
Message(const string &str =""): contents(c) { }
```
也是默认构造函数，因为它只有一个形参 str，而且这个形参有默认值（空字符串），所以它是默认构造函数






&emsp;
&emsp;
## 31.	合成的默认构造函数 什么情况下会被定义？
&emsp;&emsp; 只有在类 没有定义任何构造函数 时，那编译器才会隐式的定义一个 合成的默认构造函数（synthesized default constructor）。






&emsp;
&emsp;
## 32.	合成的默认构造函数 的初始化规则是怎样的？
如果 **存在** 类内的初始值，则用它来初始化成员；
如果 **没有** 类内的初始值，执行默认初始化。
```cpp
struct Sales_data {  
    std::string isbn() const { return bookNo; }  
    Sales_data& combine(const Sales_data&);  
    double avg_price() const;  
    std::string bookNo;        // 无类内初始值，将执行默认初始化（空串）  
    unsigned units_sold = 0;  // 有类内初始值：0，将用来初始化units_sold
    double revenue = 0.0;     // 有类内初始值：0.0，将用来初始化revenue 
};  
```






&emsp;
&emsp;
## 33.	什么样的类适使用 合用默认构造函数？
&emsp;&emsp; 如果类包含的 所有内置类型、复核类型 的成员被赋予类内初始值时，这个类才适合使用合成的默认构造函数。






&emsp;
&emsp;
## 34.	哪些类 不能依赖于编译器 合成的默认构造函数？为什么？
1 ) 已经定义构造函数的类
如果类内已经显式地定义了构造函数，则编译器不会再合成默认构造函数，也就是说：若不自力更生（自己定义一个默认构造函数），那这个该类将没有默认构造函数：
下面的类Sales_data没有构造函数：
```cpp
struct Sales_data {  
    // 没有 默认构造函数
    //① 自己定义了构造函数； ② 自己没有显示定义默认构造函数
    Sales_data(const std::string &s): bookNo(s) { }  
    // 下面省略了成员函数和数据成员  
    略...
};  
```
和上面的不一样，下面的类Sales_data 有构造函数：
```cpp
struct Sales_data {  
     // 虽然编译器不再生成默认构造函数，但自己显示的定义了默认构造函数
    Sales_data() = default;  
    Sales_data(const std::string &s): bookNo(s) { }  
    // 下面省略了成员函数和数据成员  
    // 略...
};  
```
2 ) 未给内置类型、复合类型 赋 类内初始值 的类
其中，内置类型指的是：int等类型，复合类型指的是：数组、指针等类型
我们知道，在定义内置类型、复合类型变量的时候，如果没有指定初值，则变量将被默认初始化，此时变量被赋予了“默认值”，默认初始化的规则为：
(全局变量) 的变量被初始化为0；·
(局部变量) 将不被初始化，此时的值是未定义的（除局部static变量）
因此如果用编译器合成的默认构造函数，用户在创建类对象的时候就可能得到未定义的值，这样是很危险的。
3 ) 有时，编译器不能为某些类合成默认的构造函数。
如果类内包含一个其他类类型的成员，而且该成员的类型没有默认构造函数，则无法初始化。此时我们必须自定义默认构造函数，否则将没有可用的默认构造函数。
```cpp
class NoDefault {  
public:  
     // 已有构造函数 且未显示定义默认构造函数、也未用 =default要求编译器合成
     // 所以类NoDefault 没有 默认构造函数
    NoDefault(const std::string&);  
    int len = 0;  
};  
struct A {  
    NoDefault my_mem;  // 注意！类NoDefault 没有 默认构造函数！
    double balance = 0.0;  
};  
A a; // 错误: 类A的成员my_mem的类型是类NoDefault，而它没有，默认构造函数，因此编译器不能为其合成默认构造函数
```






&emsp;
&emsp;
## 35.	在effective c++中指出：惟有默认构造函数”被需要“的时候编译器才会合成默认构造函数，怎么理解 “ 被需要 ”？
. TODO:






&emsp;
&emsp;
## 36.	`Sales_data() = default;`中后面的`=default` 是什么意思？
1 ) 该构造函数不接受任何实参，所以它是一个默认构造函数
2 ) 在参数列表后写上 “=default”，意思是 要编译器生成一个默认构造函数，这个函数和之前的 合成默认构造函数 的作用完全相同






&emsp;
&emsp;
## 37.为什么需要 `=default `？
背景：
	C++ 的类有四类特殊的成员函数，分别为：默认构造函数，析构函数，拷贝构造函数以及拷贝赋值函数（TODO：书上说（13.1.5）是默认构造函数或拷贝控制成员，这样一来就有五个，等看到13章再回来）。如果程序没有显式地为一个类定义某个特殊成员函数，而又需要用到该特殊成员函数时，编译器会隐式地为这个类生成一个默认的特殊成员函数。
下面的代码虽然没有定义构造函数，但编译器隐式生成默认构造函数，所以它能编译通过：
```cpp
class X {  
private:  
    int a;  
};  
X x;   //可以编译通过，编译器隐式生成默认构造函数  
```
但下面的代码会出现编译错误，因为如果类自己显式的定义了构造函数，编译器是不会为类生成默认构造函数的： 
```cpp
class X{  
public:  
    X(int i){  
        a = i;  
    }  
private:  
    int a;  
};  
  
X x; // 错误，类自己显式的定义了构造函数，编译器是不会为类生成默认构造函数，因此默认构造函数 X::X()不存在   
```
为了解决上面的问题，我们需要自定义默认构造函数，如下代码可正常运行：
```cpp
class X{  
public:  
    X(){};  // 手动定义默认构造函数  
    X(int i){  
        a = i;  
    }  
private:  
    int a;  
};  
  
X x; //正确，自己定义了默认构造函数 X::X()   
```
但是手动编写存在两个问题：
1. 程序员工作量变大 
2. 没有编译器自动生成的默认特殊构造函数效率高。
为了解决上述问题，可以在函数后面加上 “ `=default` ”，把工作交给编译器，相当于 主动让编译器为你生成合成默认构造函数（析构函数，拷贝构造函数以及拷贝赋值函数 同理）






&emsp;
&emsp;
## 38.	 哪些函数可以使用 “ =default ” ？
只能对具有合成版本的成员函数使用“ =default ”（即，默认构造函数或拷贝控制成员），TODO:
```cpp
class Sales_data {  
public:  
    // copy control; use defaults  
    Sales_data() = default;  
    Sales_data(const Sales_data&) = default;  
    Sales_data& operator=(const Sales_data &);  
    ~Sales_data() = default;  
    // other members as before  
};  
Sales_data& Sales_data::operator=(const Sales_data&) = default;  
```






&emsp;
&emsp;
## 39.	“ =default ”的使用规则？
1) “ =default ” 仅用于类的特殊成员函数，且该特殊成员函数没有默认参数。例如：
```cpp
class X {  
public:  
    int f() = default: 		// 错误， f()非特殊成员函数  
    X(int) = default; 			// 错误， 非特殊成员函数  
    X(int i = 1) = default; 	// 错误， 含有默认参数  
}  
```
2) 可以在类内使用=default，也可以在类外：
可以在类内使用=default，这样合成的函数将隐式的声明为内联（就像其它定义都在类内的成员函数一样）；
也可以在类外使用=default，这样就相当于定义在类外，即合成的函数就不是内联的了。
```cpp
class Sales_data {  
public:  
    Sales_data() = default;  // 隐式声明为内联函数
    Sales_data(const Sales_data&) = default;  // 隐式声明为内联函数
    Sales_data& operator=(const Sales_data &);  // 隐式声明为内联函数
    ~Sales_data() = default;  // 隐式声明为内联函数
    // other members as before  
};  
Sales_data& Sales_data::operator=(const Sales_data&) = default; //非内联函数
```






&emsp;
&emsp;
## 40.	什么是构造函数初始值列表？
&emsp;&emsp; 构造函数初始化列表 以一个冒号开始，接着是以逗号分隔的数据成员列表，最后跟着一对空花括号，如下面的第二和第三个构造函数：
```cpp
struct Sales_data {  
    // constructors added  
    Sales_data() = default;   
    Sales_data(const std::string &s): bookNo(s) { }  
    Sales_data(const std::string &s, unsigned n, double p):  bookNo(s), units_sold(n), revenue(p*n) { }  
    Sales_data(std::istream &);  
    // other members as before  
    std::string isbn() const { return bookNo; }  
    Sales_data& combine(const Sales_data&);  
    double avg_price() const;  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
}; 
```
### 40.1 `Sales_data(const std::string &s): bookNo(s) { }` 只提供了bookNo的值，剩下的units_sold和revenue怎么初始化？
当某个成员被初始化列表忽略时，它将以 合成默认构造函数相同的方式初始化，这上面的代码中，units_sold和revenue都有类内初始值，所以他们将 使用类内初始值初始化，因此这个构造函数相当于：
```cpp
// 等价于：Sales_data(const std::string &s): bookNo(s) { }   
Sales_data(const std::string &s):bookNo(s), units_sold(0), revenue(0.0){ }  
```






&emsp;
&emsp;
## 41.	在构造函数中，初始化发生在什么时候？
&emsp;&emsp; 在构造函数中，成员的初始化发生在 函数体执行前，且按照成员在类中出现的顺序进行初始化。






&emsp;
&emsp;
## 42.	使用构造函数初始化列表中初始化 和 在构造函数内部赋值 有区别吗？
&emsp;&emsp; 有的，初始化列表是 初始化，而在构造函数内部是 赋值，对于内置类型来说初始化和赋值结果都一样，但是对于 引用 和 `const`成员，在内部赋值显然是不可以的。






&emsp;
&emsp;
## 43.	哪些成员不能在构造函数内部赋值？
&emsp;&emsp; 因为在构造函数内部进行的是 赋值操作，而有些类型是不能赋值，只能初始化的：引用成员变量、const成员变量，因此它们必须在 初始化列表中进行初始化； 
&emsp;&emsp; 某些类类型成员：该类没有默认构造函数，不能进行默认初始化，它们也必须在初始化列表中进行初始化。
```cpp
#include <iostream>  
  
using namespace std;  
  
class test{  
public:  
    test(int , int &, const double);  
    int a;  
    int &rf;  
    const double d;  
};  
  
test::test(int a_, int &rf_, const double d_)  
{  
    a = a_;  
    rf = rf_;  
    d = d_;  
}  
  
int main()  
{  
    int rf = 2;  
    test(1, rf, 3.0);  
  
    return 0;  
}  
```
编译运行，结果为：
```
test.cpp: In constructor ‘test::test(int, int&, double)’:
test.cpp:14:1: error: uninitialized reference member in ‘int&’ [-fpermissive]
 test::test(int a_, int &rf_, const double d_)
 ^~~~
test.cpp:10:10: note: ‘int& test::rf’ should be initialized
     int &rf;
          ^~
test.cpp:14:1: error: uninitialized const member in ‘const double’ [-fpermissive]
 test::test(int a_, int &rf_, const double d_)
 ^~~~
test.cpp:11:18: note: ‘const double test::d’ should be initialized
     const double d;
                  ^
test.cpp:18:9: error: assignment of read-only member ‘test::d’
     d = d_;
         ^~
```






&emsp;
&emsp;
## 44.	初始化列表的成员初始化顺序 是怎样的？
&emsp;&emsp; 成员变量在类中的声明次序 就是 其在初始化列表中的初始化顺序，与其在初始化列表中的先后次序无关，所以要特别注意，保证两者顺序一致才能真正保证其效率和准确性。






&emsp;
&emsp;
## 45.	可以用一个成员 来初始化另一个成员吗？
&emsp;&emsp; 可以，但是要确保该成员比被初始化的成员后初始化。






&emsp;
&emsp;
## 46.	哪些函数可能拥有编译器合成版本的函数？






&emsp;
&emsp;
## 47.	为什么需要 访问说明符（access specifiers）？
&emsp;&emsp; 为了数据封装。






&emsp;
&emsp;
## 48.	有哪些访问说明符？
**public：**
&emsp;&emsp; 公有成员变量、公有成员函数，它可以在被的外部被访问，public成员一般定义类的接口。
**private：**
&emsp;&emsp; 私有成员变量、私有成员函数，它们在类的外部是不可访问的，甚至是不可查看的。只有类的成员函数和友元函数可以访问私有成员，类的使用者不能访问私有成员（非成员函数也不能访问private成员，除非该非成员函数声明为该类的友元）。private封装了（隐藏了）类的实现细节。
**protected** ：
&emsp;&emsp; 保护成员变量或函数 与私有成员十分相似，但有一点不同，保护成员在派生类（即子类）中是可访问的。






&emsp;
&emsp;
## 49.	访问说明符的有效范围
&emsp;&emsp; 从访问说明符A出现开始，直到下一个访问说明符B出现，在这期间是访问说明符A的有效范围。






&emsp;
&emsp;
## 50.	同一个访问说明符在一个类里可以出现多次吗？
&emsp;&emsp; 可以出现多次。






&emsp;
&emsp;
## 51.	使用class和struct有何区别
它们唯一的区别是：默认访问权限不一样
&emsp;&emsp; **class**：在第一个访问说明符出现之前，成员都是private的
&emsp;&emsp; **strut**：在第一个访问说明符出现之前，成员都是public的






&emsp;
&emsp;
## 52.	为什么需要友元
&emsp;&emsp; 有一些类虽然不是类的成员（比如说 非成员函数），但也是类的一部分，为了让类的非成员函数可以访问 类的非公有成员（包括private和public）。






&emsp;
&emsp;
## 53.	什么可以成为友元？
&emsp;&emsp; 友元可以是一个函数，该函数被称为友元函数；
&emsp;&emsp; 友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元；






&emsp;
&emsp;
## 54.	关于友元
.TODO:






&emsp;
&emsp;
## 55.	如何声明一个友元函数？
1 ) 在类的内部，在函数声明前面加一个friend关键字即可（建议在类定义的开始或结束的位置集中声明友元）；
2 ) 在外面再次声明友元函数
```cpp
class Sales_data {  
// friend declarations for nonmember Sales_data operations added  
friend Sales_data add(const Sales_data&, const Sales_data&);  
friend std::istream &read(std::istream&, Sales_data&);  
friend std::ostream &print(std::ostream&, const Sales_data&);  
// other members and access specifiers as before  
public:  
    /* 
        公有成员 
    */  
private:  
    /* 
        私有成员 
    */ 
};  
// declarations for nonmember parts of the Sales_data interface  
Sales_data add(const Sales_data&, const Sales_data&);  
std::istream &read(std::istream&, Sales_data&);  
std::ostream &print(std::ostream&, const Sales_data&);  
```






&emsp;
&emsp;
## 56.	友元的声明在类内出现的位置有限制吗？
&emsp;&emsp; 没有，友元必须声明在类的内部，但在类内部的具体位置没有要求，因为友元并不是类的成员，这意味着它不受它所在区域的访问控制级别的约束（声明在private区域也是可以的），但建议在类定义的开始或结束的位置集中声明友元。






&emsp;
&emsp;
## 57.	什么是 友元类？
&emsp;&emsp; 友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。






&emsp;
&emsp;
## 58.	如何将成员函数设为内联函数？
(1) 将成员函数定义在类的内部，这样该成员函数将隐式的被声明为`inline`函数；
(2) 在类的内部显式声明为`inline`，但在定义的时候不加`inline`：
```cpp
class Screen {  
public:  
    /* 
      其它成员 
    */  
    inline char get(pos ht, pos wd) const; // 类内声明为inline  
private:  
    /* 
      其它成员 
    */  
};  
// 虽然定义的时候没有inline说明符，但已经在类的内部声明为inline了；
char Screen::get(pos r, pos c) const   
{  
    pos row = r * width;      // compute row location  
    return contents[row + c]; // return character at the given column  
}  
```
(3) 在类的内部没有显式声明为`inline`，但在类外部定义的时候加上了inline运算符：
```cpp
class Screen {  
public:  
    /* 
      其它成员 
    */  
    Screen &move(pos r, pos c); // 类内声明没有inline，但在类外定义的时候加上了
private:  
    /* 
      其它成员 
    */  
};  
// 虽然类内没有声明为inline，但在定义的时候加上了inline  
inline Screen &Screen::move(pos r, pos c)  
{  
    pos row = r * width; // compute the row location  
    cursor = row + c;    // move cursor to the column within that row  
    return *this;        // return this object as an lvalue  
}  
```






&emsp;
&emsp;
## 59.	什么是 可变数据成员？
&emsp;&emsp; 可变数据成员（mutable data member）永远不会是const，即使它是一个const对象的成员，也就是说 可变数据成员 可被 const成员函数修改。






&emsp;
&emsp;
## 60.	为什么需要 可变数据成员？
&emsp;&emsp; 我们知道，const成员函数的const是修饰this指针的，这就意味着const成员函数不能修改 调用该成员函数的对象，但有的时候，我们又希望在const成员函数中修改某一个成员变量，此时就可以将该将该成员变量声明为 可变数据成员
```cpp
#include <iostream>  
  
using namespace std;  
  
class X  
{  
public:  
    bool GetFlag() const  // const成员函数
    {  
        m_accessCount++;  	// 正确，m_accessCount是mutable的；
        len = 6;  			// 错误，len不是mutable的；
        cout<<"len:"<<len<<endl;  
        return m_flag;  
    }  
private:  
    bool m_flag;  
    int len = 0;  
    mutable int m_accessCount;  
};  
  
int main()  
{  
    X x;  
    x.GetFlag();  
}  
```
上面的代码直接编译不过，错误为：
```
test.cpp: In member function ‘bool X::GetFlag() const’:
test.cpp:11:15: error: assignment of member ‘X::len’ in read-only object
         len = 6;     // 错误，len不是mutable的；
               ^
```






&emsp;
&emsp;
## 61.	如果一个类包含一个 类数据成员，该怎么给它赋初值比较合适？
用“=”或花括号给该类成员一个初始值：
```cpp
class Window_mgr {  
private:   
     // 初始值为 Screen(24, 80, ' ')
    std::vector<Screen> screens{Screen(24, 80, ' ') };  
};  
```






&emsp;
&emsp;
## 62.	从 const成员函数 返回 *this 需要注意什么？
&emsp;&emsp; 因为const成员函数的const是修饰 this指向的对象的，所以返回的*this 是一个常量引用，若改变 const成员函数 返回的*this 的话讲引发错误（修改const对象）：






&emsp;
&emsp;
## 63.	为什么要在 类内 对const成员函数 重载？
1 ) 我们知道，常量对象 不能调用 非常量版本的函数，因为不能用常量实参去初始化非常量的形参，所以我们需要重载const成员函数，一个接收常量对象，一个接收非常量对象，即使它们完成的工作一样。
2 ) 如果需要返回*this的话，const成员函数返回的是常量对象，这可能造成不方便，需要用一个非常量版本的函数 来返回 非常量对象；
```cpp
class Screen {  
public:  
    // 根据对象是否const 重载了display函数  
    Screen &display(std::ostream &os)  
        { do_display(os); return *this; }  
   // const成员函数返回的是 const对象
    const Screen &display(std::ostream &os) const  
        { do_display(os); return *this; }  
private:  
    // function to do the work of displaying a Screen  
    void do_display(std::ostream &os) const {os << contents;}  
    // other members as before  
};  
```
上述代码为什么要费力单独定义一个do_display函数呢？
这样代码比较统一，在项目里，同样的功能最好用同样的代码来完成，这样后面修改也比较简单；






&emsp;
&emsp;
## 64.	如何新建一个类对象？
加不加class都可以：
```cpp
Sales_data item1; 		  // 不加class  
class Sales_data item1; // 也可以加上class，这等价于上面的变量声明  
```






&emsp;
&emsp;
## 65.	在类被 声明之后，定义之前，可以新建该类的对象吗？
不能，对于一个类来说，在外面创建它的对象之前，该类必须被定义，而不能仅仅是被声明。
为什么呢？
因为没有定义的话，编译器就不知道这个类占了多少空间，自然不能用来新建对象。
有什么折中的办法吗？
在类被 声明之后，定义之前，我们可以新建指向该类的指针，这样也能完成任务。






&emsp;
&emsp;
## 66.	想在类A中修改类B的私有成员，应该怎么做？
1 ) 将 类A 声明为类B的友元类；
2 ) 将 类A中需要修改类B的函数 声明为类B的友元函数；






&emsp;
&emsp;
## 67.	如何将 一组重载函数 都 声明为类的友元？
&emsp;&emsp; 虽然重载函数的函数名相同，但实际上它们是不同的函数，要想将一组重载函数 都 声明为类的友元，需要为这组函数中的每一个函数分别声明为friend。
对于下面的代码，只有第一个才是Screen类的友元：
```cpp
extern std::ostream& storeOn(std::ostream &, Screen &); //它是Screen类的友元
extern BitMap& storeOn(BitMap &, Screen &);  //它不是Screen类的友元
class Screen {  
    // ostream version of storeOn may access the private parts of Screen objects  
    friend std::ostream& storeOn(std::ostream &, Screen &);  
    // . . .  
};  
```






&emsp;
&emsp;
## 68.	友元函数可以定义在 类的内部吗？
&emsp;&emsp; 可以，定义在类内部的友元函数也将是隐式内联的。






&emsp;
&emsp;
## 69.	友元的声明 和 普通意义上的声明 有何区别？
&emsp;&emsp; 友元声明的作用 仅仅是 影响 友元函数 对类的访问权限，它本身意义并不是普通意义上的声明（想要在类内使用该友元函数，需要在外面声明它，好让它对成员函数可见）。
&emsp;&emsp; 也就是说如果类内的成员函数想使用该友元函数，则必须在外面声明该函数，要不然对于类内的函数来说该友元函数是不可见的（即使该友元函数定义在类内）。
下面的代码是错的，因为函数 X()、g()在函数f()被声明之前使用了它：
```cpp
struct X {  
    friend void f()  // 友元的声明，对于后面的X()来说，它是不可见的
    { 
        /* 定义在内部的友元函数 */ 
    }  
    X() { f(); } // 错误: 函数f()还未被声明  
    void g();  
    void h();  
};  
void X::g() { return f(); } 	// 错误: 函数f()还未被声明   
void f(); 					//此处为f()的声明   
void X::h() { return f(); } 	// 正确: 函数f()在上一句声明了  
```
想要修改上面的代码很容易，把 void f()  放到struct X前面，其它不变即可。






&emsp;
&emsp;
## 70.	定义在类外部的函数 的返回值前面 需要加类名吗？
因为类名只能保证它后面的部分（参数列表、函数体）的作用域在类的作用域，例如对于下面的代码：
```cpp
void Window_mgr::clear(ScreenIndex i)  
{  
    Screen &s = screens[i];  
    s.contents = string(s.height * s.width, ' ');  
} 
```
Window_mgr::只能保证函数名clear、以及它的参数列表、它的函数体在类Window_mgr的作用域内，而前面的返回类型 void ，并不受Window_mgr::的作用，因为返回类型 void在他前面。也就是说，如果返回值类型是类自己定义的，那就需要，如果不是那就不需要，只要保证编译器能找到就行。
对于下面的例子：
因为类型ScreenIndex 是类Window_mgr内部定义的，因此需要加上类名，告诉编译器，这里的类型ScreenIndex 是取自类Window_mgr：
```cpp
class Window_mgr {  
public:  
    // 自己定义了类型 ScreenIndex 
    using ScreenIndex = std::vector<Screen>::size_type;  
    ScreenIndex addScreen(const Screen&);  
    // other members as before  
};  
// 下面有两个  类名(Window_mgr::)
// 第一个的作用：告诉编译器 类型ScreenIndex  是取自类Window_mgr
// 第二个的作用：告诉编译器 addScreen()函数是类Window_mgr的成员
Window_mgr::ScreenIndex  
Window_mgr::addScreen(const Screen &s)   
{  
    screens.push_back(s);  
    return screens.size() - 1;  
}  
```






&emsp;
&emsp;
## 71.	编译器怎么处理 类的成员函数的 声明 和 定义？
第一步：编译所有成员函数的声明；
第二步：直到整个类的声明都处理完后，处理函数体（即定义）
### 71.1 为什么编译器要这么做呢？
这样可以简化类代码的组织方式，因为编译器先处理所有的声明，这就意味着，先声明的成员 也可以使用 后声明的成员。
### 71.2 有例外情况吗？
	有的，类型别名 在使用前必须被声明，如果某个成员使用了类中还没出现的名字，则编译器会在外层作用域查找
```cpp
typedef double Money;  
string bal;  
class Account {  
public:  
    Money balance() { return bal; }  // 用的是类外的 类型别名Money
private:  
    Money bal;  // 用的是类外的 类型别名Money
    // ...  
};  
```






&emsp;
&emsp;
## 72. 在类内 定义类型别名 需要注意什么？
&emsp;&emsp; 假设在类内定义了类型别名A，且在类的外层也有某个类型别名B和类型别名A 同名，若在类内使用了类型别名B，则类型别名A的声明将报错，错误原因是重复定义了相同的类型别名。
下面的代码编译时将报错：
```cpp
#include <iostream>  
  
using namespace std;  
  
typedef double Money;  
  
class Account {  
public:  
    Money balance() { return bal; } // 用的是类外的 类型别名Money  
private:  
    // 前面的成员函数blance()使用了外层的类型别名Money，则报错
    typedef double Money; // 错误: 重复定义了  类型别名Money 
    Money bal;  
};  
  
int main()  
{  
    Account A;  
}
```
编译报错：
```
test.cpp:15:20: error: declaration of ‘typedef double Account::Money’ [-fpermissive]
     typedef double Money; // 错误: 重复定义了  类型别名Money
                    ^~~~~
test.cpp:8:16: error: changes meaning of ‘Money’ from ‘typedef double Money’ [-fpermissive]
 typedef double Money;
                ^~~~~
```
 
**编译报错的原因：**
	成员函数balance()使用了外层的类型别名Money，所以后面的的声明  typedef double Money重复定义了类型别名Money

将代码修改成下面之后，成功编译，因为将类型别名的声明移到类的最前面来了，它将隐藏 外面那个 同名的别名：
```cpp
#include <iostream>  
  
using namespace std;  
  
typedef double Money;  
  
class Account {  
public:  
    // 将类型别名的声明移到类的最前面来了，它将隐藏 外面那个 同名的别名
    typedef double Money;   
    Money balance() { return bal; }   
private:  
    Money bal;  
    // ...  
};  
  
int main()  
{  
    Account A;  
}
```






&emsp;
&emsp;
## 73.	在类内 定义类型别名 建议怎么做？
&emsp;&emsp; 把类型别名的 定义写在类的开始处，这样可以保证所有使用该类型的成员都可以使用它。






&emsp;
&emsp;
## 74.	如果在类外有一个变量名为 height，类内也有一个名为height的成员，如何在类内部使用 类外那个height呢？
用作用域运算符来请求外部的那个height，代码如下：
```cpp
int height; // 全局变量  
class Screen {  
public:  
    typedef std::string::size_type pos;  
    void dummy_fcn(pos height) {  
    cursor = width * height; // which height? the parameter  
    }  
private:  
    pos cursor = 0;  
    pos height = 0, width = 0;  
};  
void Screen::dummy_fcn(pos height) {  
    cursor = width * this->height; 		// 成员变量height   
    cursor = width * Screen::height; 	// 成员变量height  
    cursor = width * ::height;			// 全局变量height  
}  
```






&emsp;
&emsp;
## 75.	初始化 类数据成员 有哪些方法？
&emsp;&emsp; 随着构造函数开始执行，初始化就完成了，也就是说初始化类的数据成员的唯一机会就是通过 构造函数初始化列表。






&emsp;
&emsp;
## 76.	使用 构造函数初始值 效率会更高吗？
&emsp;&emsp; 是的，构造函数初始化列表 是直接初始化成员，而在构造函数内部赋值是先初始化，然后赋值，相比之下多了一个赋值的操作，效率确实不如构造函数初始化列表。






&emsp;
&emsp;
## 77.	下面的代码有什么问题？
```cpp
class X {  
    int i;  
    int j;  
public:  
    // undefined: i is initialized before j   
    X(int val): j(val), i(j) { }  
}; 
```
因为构造函数初始化成员的顺序并不是按初始值列表出现的顺序来进行的，而是根据数据成员在类内声明的顺序来初始化的，所以在上述的代码中，成员i比j先初始化，因此在构造函数 X(int val): j(val), i(j) { }中，用j来初始化i，因此这段代码存在的问题是：用未初始化的变量j来初始化i。






&emsp;
&emsp;
## 78.	什么是 委托构造函数（delegating constructor）？
&emsp;&emsp; 顾名思义，委托构造函数 是 把自己的  一些（甚至全部）工作（初始化数据成员）  “委托”给 其它构造函数，






&emsp;
&emsp;
## 79.	如何编写 委托构造函数？
和其它构造函数一样， 委托构造函数 也有 成员初始值列表 和 函数体，例如：
```cpp
Sales_data(std::istream &is): Sales_data()  
                   { read(is, *this); }
    /*成员初始值列表*/	 ：Sales_data()
    /*函数体*/		    :{ read(is, *this); }
```
```cpp
class Sales_data {  
public:  
    // 第一个构造函数：它是 非委托构造函数 使用对应的实参初始化成员：  
    Sales_data(std::string s, unsigned cnt, double price):  
                bookNo(s), units_sold(cnt), revenue(cnt*price) {}  

     // 下面都是 委托构造函数

     // 第二个构造函数：它是 默认构造函数，委托给了第一个构造函数
    Sales_data(): Sales_data("", 0, 0) {}  

     // 第三个构造函数：委托给了第一个构造函数
    Sales_data(std::string s): Sales_data(s, 0,0) {}  

    // 第四个构造函数：先委托给了 默认构造函数，然后 默认构造函数 又委托给了 第一个构造函数；
    // 受委托的 托构造函数执行完之后，开始执行函数体  read(is, *this)
    Sales_data(std::istream &is): Sales_data()  
                    { read(is, *this); }  
    // other members as before  
};  
```






&emsp;
&emsp;
## 80.	默认构造函数 什么时候会被调用？
&emsp;&emsp; 当对象被 默认初始化 或 值初始化 的时候，会自动执行默认构造函数






&emsp;
&emsp;
## 81.	下面的代码存在什么问题？
```cpp
class NoDefault {  
public:  
    NoDefault(const std::string&);  
    int len = 0;  
};  
struct A {   
    NoDefault my_mem;  
    double balance = 0.0;  
};  
A a;  
struct B {  
    B() {}   
    NoDefault b_member;  
};  
```
① 类NoDefault 是没有默认构造函数的，因为类NoDefault 已经有一个构造函数了，而它没有要求编译器给它生成（使用 =default），因此编译器不会帮它合成默认构造函数；
② 如果类内包含一个其他类类型的成员，而且该成员的类型没有默认构造函数，则无法初始化。此时我们必须自定义默认构造函数，否则将没有可用的默认构造函数。
而类A有一个成员是类NoDefault ，而它没有 默认构造函数，因此编译器不能为其合成默认构造函数，具体看下面的注释：
```cpp
class NoDefault {  
public:  
     // 已有构造函数 且未显示定义默认构造函数、也未用 =default要求编译器合成
     // 所以类NoDefault 没有 默认构造函数
    NoDefault(const std::string&);  
    int len = 0;  
};  
struct A {  
    NoDefault my_mem;  // 注意！类NoDefault 没有 默认构造函数！
    double balance = 0.0;  
};  
A a; // 错误: 类A的成员my_mem的类型是类NoDefault，而它没有 默认构造函数，因此编译器不能为其 合成默认构造函数
struct B {  
    B() {} // 错误:  b_member没有初始值  
    NoDefault b_member;  
};  
```






&emsp;
&emsp;
## 82.	什么是 转换构造函数（converting constructor）？
&emsp;&emsp; 如果构造函数只接受一个参数，则它实际上定义了转换为此类类型的隐式转换机制，有时我们把这种构造函数称为转换构造函数（converting constructor）






&emsp;
&emsp;
## 83.	如何定义 隐式的 类类型转换？(什么样的函数可以进行隐式的类类型转换？)
&emsp;&emsp; 如果构造函数只接受一个参数，则它实际上定义了转换为此类类型的隐式转换机制，有时我们把这种构造函数称为转换构造函数（converting constructor）
&emsp;&emsp; 能通过一个实参调用 的构造函数 定义了一条 从构造函数的参数类型 向类类型 隐式转换的规则。






&emsp;
&emsp;
## 84.	下面有哪些是 转换构造函数？
```cpp
class Sales_data {  
public:  
    // constructors  
    Sales_data() = default;  
    Sales_data(const std::string &s): bookNo(s) { }  
    Sales_data(const std::string &s, unsigned n, double p):  
               bookNo(s), units_sold(n), revenue(p*n) { }  
    Sales_data(std::istream &);  
  
    // operations on Sales_data objects  
    std::string isbn() const { return bookNo; }  
    Sales_data& combine(const Sales_data&);  
    double avg_price() const;  
private:  
    std::string bookNo;  
    unsigned units_sold = 0;  
    double revenue = 0.0;  
};  
```
### 84.1 哪些是？
下面这两个是，因为它们都是 只接受一个参数的构造函数：
```cpp
① Sales_data(const std::string &s): bookNo(s) { } 
② Sales_data(std::istream &);  
```
### 84.2 对于上面的类定义，编译器在执行下面的语句的时候 发生了什么？
```cpp
string null_book = "9-999-99999-9";  
item.combine(null_book);  // 为什么传string对象进去，编译器没有报错？
```
我们来看一下`combine()`函数的函数原型：
```cpp
Sales_data& combine(const Sales_data&);
```
它接受一个Sales_data参数，并返回一个Sales_data的引用，但下面的语句在调用combine()函数的时候用的是一个string 类型：
```cpp
item.combine(null_book);
```
然而编译器却没有报错，这是因为编译器将 string 类型转换成了一个Sales_data类型的对象：
	① 调用构造函数`Sales_data(const std::string &s): bookNo(s) { }`创建一个临时变量tmp；
	② 临时变量tmp的units_sold 成员、revenue成员 等于 0 ， bookNo成员则等于变量null_book；
	③ 新生成的这个tmp变量被传给了combine()函数；
### 84.3 那下面的代码会发生什么呢？
```cpp
item.combine("9-999-99999-9");  
```
编译器只会执行一步类型转换，而上面的代码执行了两步，分别是：
(1)  将"9-999-99999-9" 转换为string 类型； 
(2)  将string类型转换为 Sales_data类型；
因此这条语句将报错。
### 84.4 下面的代码呢？
```cpp
item.combine(string("9-999-99999-9"));  
item.combine(Sales_data("9-999-99999-9"));  
item.combine(cin);  
```
(1) `item.combine(string("9-999-99999-9"))`：
> ① "9-999-99999-9"显式 地转换为string；
> ②  string隐式的转换为Sales_data；
> 
也就是说只执行了一步 隐式 类型转换，因此是正确的；

(2) `item.combine(Sales_data("9-999-99999-9"))`：
> ① "9-999-99999-9" 隐式 地转换为string；（注意，上一句是显示的转换为string）
> ② string 显式 的转换为Sales_data；
> 
注意上面发的是隐式还是显式类型转换。

(3) `item.combine(cin)`：
> 隐式的将cin转换成Sales_data，这个转换
> 






&emsp;
&emsp;
## 85.	如何抑制 构造函数定义的 的隐式转换？
通过将 构造函数 声明为`explicit`加以阻止。
```cpp
class Sales_data {  
public:  
    Sales_data() = default;  
    Sales_data(const std::string &s, unsigned n, double p):  
                bookNo(s), units_sold(n), revenue(p*n) { }  
    explicit Sales_data(const std::string &s): bookNo(s) { }  
    explicit Sales_data(std::istream&);  
    // remaining members as before  
}; 
```






&emsp;
&emsp;
## 86.	编写和使用 explicit构造函数 时要注意什么？
&emsp;&emsp; (1) 关键字`explicit`只对于 实参数量为1个的构造函数 有效，因为多个实参的构造函数不能用于隐式转换，所以没必要将这些构造函数指定为`explicit`；
&emsp;&emsp; (2) 只能在类内声明构造函数的时候加`explicit`关键字，在类外定义的时候不应该加`explicit`：
```cpp
class Sales_data {  
public:  
    Sales_data() = default;  
    explicit Sales_data(const std::string &s): bookNo(s) { }
    explicit Sales_data(std::istream&);  
    // remaining members as before  
};  

// 错误，类外定义的生活不应该加 explicit关键字  
explicit Sales_data::Sales_data(istream& is)  {  
    read(is, *this);  
}  
```
&emsp;&emsp; (3) `explicit`构造函数 只能用于显示初始化，不能用于拷贝形式的初始化
```cpp
string null_book = "9-999-99999-9";  
// 错误:  
Sales_data item2 = null_book;  
```
错误的原因：
> &emsp;&emsp; 因为null_book是一个string类型的变量，而将null_book赋给Sales_data类型的item2 ，这意味着它将调用构造函数:
> 
```cpp
explicit Sales_data(const std::string &s): bookNo(s) { }
```
来进行类型转换，而这个构造函数是`explicit` 的，所以会报错






&emsp;
&emsp;
## 87. 什么是 explicit构造函数？
&emsp;&emsp; 在只有一个实参的构造函数前面加`explicit`关键字，用来阻止通过该构造函数来进行隐式类型转换。






&emsp;
&emsp;
## 88. 下面的代码 正确吗？
```cpp
class Sales_data {    
public:    
    Sales_data() = default;    
    Sales_data(const std::string &s, unsigned n, double p):    
                bookNo(s), units_sold(n), revenue(p*n) { }    
    explicit Sales_data(const std::string &s): bookNo(s) { }    
    explicit Sales_data(std::istream&);    
    // remaining members as before    
};   
item.combine(static_cast<Sales_data>(cin));  
```
正确，虽然构造函数  explicit Sales_data(std::istream&); 是explicit 的，这意味不能通过它进行隐式类型转换，而通过它进行显示类型转换是可以的。






&emsp;
&emsp;
## 89.	什么是显示构造函数？
&emsp;&emsp; explicit构造函数 也称为 显示构造函数。






&emsp;
&emsp;
## 90. 标准库中有哪些 显示构造函数
The string constructor that takes a single parameter of type const char*
is not explicit.
The vector constructor that takes a size is explicit.






&emsp;
&emsp;
## 91.	什么是 聚合类（Aggregate Classes）
1 ) 所有成员都是public的（直接用struct即可）
2 ) 没有定义任何构造函数；
3 ) 没有类内初始值；
4 ) 没有基类，也没有virtual函数
这感觉有点像C语言的struct
下面这个就是聚合类：
```cpp
struct Data {  
    int ival;  
    string s;  
};  
// val1.ival = 0; val1.s = string("Anna")  
Data val1 = { 0, "Anna" };  
```
**聚合类的初始化：**
1 ) 初始值的顺序 要和 声明的顺序一致：
```cpp
// error: can't use "Anna" to initialize ival, or 1024 to initialize s 
Data val2 = { "Anna", 1024 };  
```
2 ) 若初始值列表中的元素个数少于类的成员数量，则剩下的会被值初始化：
```cpp
//  s 被值初始化了
Data val2 = { 1024 };  
```






&emsp;
&emsp;
## 92.	什么是字面值常量类？
.TODO:






&emsp;
&emsp;
## 93.	什么是 类的静态成员？
&emsp;&emsp; 不同于其它成员，类的静态成员与 类本身 直接相关，而不是与类的各个对象相关。






&emsp;
&emsp;
## 94.	为什么需要 类的静态成员？（静态成员的作用是什么？）
&emsp;&emsp; 有的时候我们会需要一些成员与 类本身 直接相关，而不是与类的各个对象相关，比如对于一个银行账户类来说，可能需要一个成员来表示基准利率：
① 因为每个人的基准利率都是一样的，从效率上来说，我们没必要为每一个对象都存储相同的基础利率
② 而且万一基准利率有调整，我们希望能做到一改全改；
此时使用静态成员就能做到。






&emsp;
&emsp;
## 95. 如何声明静态成员？
在 类内 将你想声明为静态的成员、函数前面加上static关键字即可：
```cpp
class Account {  
public:  
    void calculate() { amount += amount * interestRate; }  
    static double rate() { return interestRate; }  
    static void rate(double);  
private:  
    std::string owner;  
    double amount;  
    static double interestRate;  
    static double initRate();  
};  
```
### 95.1 上面声明的类 的对象 有几个成员？
它的对象包含两个数据成员amount、owner，因为interestRate是静态的，他不和对象关联，而是与类本身关联。
### 95.2 如何定义 上面的类的 静态成员函数？
和其它成员函数一样，成员函数能在类内定义也能字类外定义，但在外面定义静态成员函数的时候，不能加static关键字：
类外定义的时候要带上 类名：
```cpp
//错误：在外面定义 静态成员函数的时候不能出现 static关键字。   
static void Account::rate(double newRate){ interestRate = newRate; }  
// 正确  
void Account::rate(double newRate){ interestRate = newRate; }  
```
### 95.3 如何定义 上面的类的 静态成员变量？
类的静态成员变量有两种定义方式：
① 在类外定义：
	因为静态成员属于整个类，而不属于某个对象，如果在类内初始化，会导致每个对象都包含该静态成员，这是矛盾的，因此
```cpp
// 注意，这是定义，不是赋值！
// 和静态成员函数一样，类外定义的时候不需要加 static  
double Account::interestRate = initRate();  
```
② 提供类内初始值：
	还有一种方法是提供一个类内初始值，这个时候要求静态成员变量的类型是 字面值常量类型constexptr：
```cpp
class Account {  
public:  
    static double rate() { return interestRate; }  
    static void rate(double);  
private:  
    static constexpr int period = 30;// period 是车辆表达式！
    double daily_tbl[period];  
};  
```
即使一个静态成员在类内部被初始化了，通常情况下也应该在类的外部定义一下该成员，而且若提供了类内初始值，则在类外定义的时候不能再指定一个初始值了：
```cpp
constexpr int Account::period;  
```






&emsp;
&emsp;
## 96. 静态成员函数 和 普通的成员函数有何区别？
&emsp;&emsp; 因为静态成员函数不和任何对象绑定在一起，因此静态成员函数没有this指针。这意味着静态成员函数不能声明为 const成员函数，因此它们的区别总结如下：
&emsp;&emsp; ① 静态成员函数没有 this指针；
&emsp;&emsp; ② 静态成员函数不能声明为const成员函数，因为它没有this指针。






&emsp;
&emsp;
## 97. 静态成员变量 的生命周期？
&emsp;&emsp; 静态成员变量和其它staic变量一样，存在于程序的整个生命周期。






&emsp;
&emsp;
## 98. 类的 普通数据成员 可以做默认实参吗？
&emsp;&emsp; 普通成员不能作为默认实参，因为普通数据成员的本身属于类对象的一部分，这么做的结果是无法真正提供一个对象以便从中获取成员的值，最终引发错误。






&emsp;
&emsp;
## 99. 哪些场景下，静态成员能使用，而普通成员不能？
静态成员独立于任何对象。因此，在某些非静态数据成员可能非法的场合，静态成员却可以正常地使用:
&emsp;&emsp; ① 静态数据成员可以是不完全类型。特别的，静态数据成员的类型可以就是它所属的类类型。而非静态数据成员则受到限制，只能声明它所属的指针或引用：
```cpp
class Bar {  
public:  
    // ...  
private:  
    static Bar mem1;     	// 正确: 静态成员可以是不完全类型
    Bar *mem2; 			// 正确: 普通成员可以是不完全类型的指针
    Bar mem3; 			// 错误: 普通成员不能是可以是不完全类型
};  
```
&emsp;&emsp; ② 可以用 静态数据成员 作为默认实参，而普通成员不能作为默认实参，因为普通数据成员的本身属于类对象的一部分，这么做的结果是无法真正提供一个对象以便从中获取成员的值，最终引发错误：
```cpp
class Screen {  
public:  
    // bkground refers to the static member  
    // declared later in the class definition  
    Screen& clear(char = bkground);  
private:  
    static const char bkground;  
};  
```






&emsp;
&emsp;
## 100 静态成员变量 的初始化 需要注意的事项？
(1) 非`const`的`static`成员变量 不能在类内初始化：
```cpp
class Demo{
public:
    static int a = 0;
private:
    //...
};

int main()
{
    Demo d;
}
```
编译时报错：
```
test.cpp:11:16: error: ISO C++ forbids in-class initialization of non-const static member ‘Demo::a’
     static int a = 0;
                ^
```
我们把`a`的初始化放在类外试试看：
```cpp
class Demo{
public:
    static int a ;
private:
    //...
};

int Demo::a = 0;


int main()
{
    Demo d;
}

```
顺利通过编译，我们再把`a`设为const试试：
```cpp
class Demo{
public:
    static const int a = 0;
private:
    //...
};

int main()
{
    Demo d;
}
```
顺利通过编译






&emsp;
&emsp;
## 101 静态成员函数 和 普通成员函数在访问权限上有何区别？为什么？
### 101.1 能调用哪些？
&emsp;&emsp; 普通成员函数可以访问所有成员（包括成员变量和成员函数），静态成员函数只能访问静态成员。
### 101.2 为什么？
&emsp;&emsp; 因为静态成员函数的作用就是为了处理静态成员。而静态成员和静态成员函数是没有this指针的，不知道指向哪个对象。既然连指向哪个对象都不清楚，当然就不能调用非静态成员了。
### 101.3 实例验证
```cpp
class Demo{
public:
    static int a;
    int b = 0;
    static int func(){
        return b;
    }
private:
    //...
};

int Demo::a = 0;

int main()
{
    Demo d;
}
```
编译时报错：
```
test.cpp: In static member function ‘static int Demo::func()’:
test.cpp:14:16: error: invalid use of member ‘Demo::b’ in static member function
         return b;
                ^
test.cpp:12:13: note: declared here
     int b = 0;
             ^
```





&emsp;
&emsp;
## 102 为什么静态成员和静态成员函数没有this指针？
&emsp;&emsp; this指针的值是当前被调用的成员函数所在的对象的起始地址，而静态成员只属于类，不属于对象，也就没有this指针了。





&emsp;
&emsp;
## 103 静态成员函数与普通成员函数的根本区别是什么？
&emsp;&emsp; 静态成员函数与普通成员函数的根本区别在于：普通成员函数有 this 指针，可以访问类中的任意成员；而静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）



&emsp;
&emsp;
## 104 静态成员占多少空间？
`static`成员 不占用类对象的空间：
```cpp
class Demo_1{
public:
    static int a;
    int b = 0;
    static int func(){
        return a;
    }
private:
    //...
};


class Demo_2{
public:
    int b;
private:
    //...
};

int Demo_1::a = 0;


int main()
{
    Demo_1 d_1;
    Demo_2 d_2;
    cout << "Size of class Demo_1 : " << sizeof(d_1) << endl;
    cout << "Size of class Demo_2 : " << sizeof(d_2) << endl;
}
```
编译后运行：
```
Size of class Demo_1 : 4
Size of class Demo_2 : 4
```
**结果分析：**
&emsp;&emsp; `Demo_1`比`Demo_1`多了一个`staitc成员变量`和一个`static成员函数`，但它们的实例的大小都是 4字节，说明`staitc成员变量`和`static成员函数`都不占用类对象的空间。








