# 1. 类型转换
## 1.1 隐式类型转换
&emsp;&emsp; 隐式类型转换是自动执行的，无需显式的操作符。 隐式类型转换发生在很多地方，比如：
> 函数实参到形参的类型转换;
> 函数返回值类型的自动转换;
> 等等...
> 
### 1.1.1 数值类型转换
&emsp;&emsp; 从小整数类型(`char、short`)转换到`int`，或者从`float`转换到`double`，这种“提升型”的转换通常不会造成数值差异。但是下面的一些情形可能存在一些转换误差，使得编译器产生警告。
> ① 无论是转换到bool类型或者是有bool类型进行转换： false等价于0(数值类型)或者空指针(指针类型)； true则等价于其它任何数值或者由true转化为1。
> ② 浮点数转化为整数会采取截断操作，即移除小数部分。如果转换时发生了数值溢出，可能出现未定义的行为。
> ③ 负数转化为无符号类型，通常会采用二进制补码表示。 (编译器不警告有符号和无符号整数类型之间的隐式转换)：
> 
```cpp
#include<iostream>

using namespace std;

int main()
{
    int a = -1;
    unsigned int b = a;
    cout << "a : " << a << endl;
    cout << "b : " << b << endl;
}
```
运行结果：
```
a : -1
b : 4294967295
```
**结果分析：**
&emsp;&emsp; 显然，编译的时候没有给出警告，而且采用了二进制补码表示转换后的值。
### 1.1.2 指针类型转换
指针通常存在以下转换：
> ① 空指针可以转换到任意指针类型；
> ② 任意指针类型都可以转换到void* 指针；
> ③ 继承类指针可以转换到可访问的明确的基类指针， 同时不改变const或者volatile属性;
> ④ 一个C风格的数组隐式把数组的第一个元素转换为一个指针。
> 

## 1.2 强制(显示)类型转换
在C++中，强制转换可以分为两类：
> &emsp;&emsp; ① C风格的强制类型转换
> &emsp;&emsp; ② C++ 提供的强制类型转换
> 

&emsp;
## 1.3 旧有的C风格的强制类型转换
&emsp;&emsp; 此方法不推荐，因为此方法在代码中不显眼，容易被忽略， 而且旧式强制转换实际上是困难且容易出错的。
```cpp
int a = 1;
(double) a;
```

&emsp;
## 1.4 C++ 提供的强制类型转换
C++ 提供四种转换操作符来实现显式类型转换：
> ① `static_cast`
> ② `const_cast`
> ③ `reinterpret_cast`
> ④ `dynamic_cast`
> 
C++ 强制类型转换运算符的用法如下：
```cpp
// 强制类型转换运算符 <要转换到的类型> (待转换的表达式)
cast-name<type>(expression);
```
### 1.4.1 `static_cast`
#### (1) 语法
```cpp
static_cast <new_type> (expression)
```
需要注意的是，`static_cast`强制转换只会在编译时检查，但没有运行时类型检查来保证转换的安全性。同时，`static_cast`不能转换掉表达式的`const`、`volitale`或者`__unaligned`属性。

#### (2) `static_cast`是否安全
&emsp;&emsp; `static_cast`只会在编译时检查，没有运行时类型检，所以是不安全的。

#### (3) 使用场景
&emsp; 任何具有明确定义的类型转换，只要不包含底层`const`，都可以使用`static_cast`。但一般来说它有主要有如下几种用法：
* 1）用于类层次结构中基类和派生类之间指针或引用的转换
    * 进行上行转换（把派生类的指针或引用转换成基类表示）是安全的
    * 进行下行转换（把基类的指针或引用转换为派生类表示），由于没有动态类型检查，所以是**不安全**的；
* 2）用于基本数据类型之间的转换，如把`double`转换成`int`，此时强制转换运算符相当于告诉程序的读者和编译器：我们不在乎潜在的精度损失；
* 3）把空指针转换成目标类型的空指针；
* 4）把任何类型的表达式转换为`void`类型；
```cpp 
#include<iostream>

using namespace std;

int main()
{
    float f_pi=3.141592f;
    int   i_pi=static_cast<int>(f_pi); /// i_pi 的值为 3
    
    /* class 的上下行转换 */
    class Base{
        // something
    };
    class Sub:public Base{
        // something
    };
    
    //  上行 Sub -> Base
    //编译通过，安全
    Sub sub;
    Base *base_ptr = static_cast<Base*>(&sub);  
    
    // 下行 Base -> Sub
    // 编译通过，但不安全
    Base base;
    Sub *sub_ptr = static_cast<Sub*>(&base);    
}
```
**运行结果：**
&emsp;&emsp; 代码成功通过编译。

&emsp; 
### 1.4.2 `const_cast`
#### (1) 作用
&emsp; `const_cast`有如下作用：
> &emsp;&emsp; 修改（增加或去除皆可）表达式的**底层`const`属性**（而且它也是四个强制类型转换运算符中**唯一**能够**去除** `const` 属性的运算符，添加`const`属性还可以用其他转换符，如`static_const`）；
> `const_cast`也能用于移除类型的`const`、`volatile`和`__unaligned`属性，：
> 
这里复习一下什么是底层`const`和顶层`const`:
```cpp
int i = 0;
int *const p1 = &i;     // we can't change the value of p1; const is top-level
const int ci = 42;      // we cannot change ci; const is top-level
const int *p2 = &ci;    // we can change p2; const is low-level
const int *const p3 = p2; // right-most const is top-level, left-most is not
const int &r = ci;      // const in reference types is always low-level
```
总结以下其实就是：
> 顶层`const`: 对象本身是`const`，这个对象可以是任何对象类型；
> 底层`const`: 只能是指针或引用，而且该对象指向的是一个`const`；
> 

#### (2) 使用
```cpp 
type const_cast<type>(expression) // 注意，const_cast返回的是一个type类型
```
参数要求：
> `type` : 转换的目标类型，必须是一个指针或引用；
> `expression`: 和`type`参数的区别只有`const`属性；
> 
我们知道，如果一个指针指向的是`const`对象，那么我们就无法通过这个指针来修改它指向的对象的值：
```cpp 
#include<iostream>

using namespace std;

int main()
{
    int i = 1;
    const int * pi = &i;
    *pi = 100;
}
```
编译报错如下：
```
test.cpp:9:11: error: assignment of read-only location ‘* pi’
     *pi = 100;
           ^~~
```
通过`const_cast`，我们可以修改这个指针的`const`属性：
```cpp
int main()
{
    int i = 1;
    const int * pi = &i;
    int * pi2 = const_cast<int *>(pi);
    *pi2 = 100;
    cout << "i: " << i << endl;
}
```
编译后运行：
```
i: 100
```

#### (3) `const_cast`是唯一可以修改`const`属性的运算符吗？
&emsp;&emsp; 准确的说不是，但`const_cast`是唯一可以**去除** `const` 属性的运算符，添加`const`属性还可以用其他转换符，如`static_const`。

#### (4) `const_cast`可以改变表达式的`const`属性，那是不是意味着可以改变`const`对象的值？
&emsp;&emsp; 并不是，`const_cast`只负责 去掉`const`属性(cast away the const)，一旦我们去掉了某个对象的`const`属性，那么编译器就不会阻止我们对该对象进行写操作了：
```cpp
// 情况一：合法
int i = 1; // i不是常量
const int * pi = &i;
int * pi2 = const_cast<int *>(pi);
*pi2 = 100;

// 情况二：不合法：
int ci = 1; // ci是const
const int * pi3 = &ci;
int * pi4 = const_cast<int *>(pi);
*pi3 = 100; // 报错 

```
> 情况一：如果指针指向的对象本身不是一个常量，使用`const_cast`获得的写权限是合法行为；
> 情况二：如果指针指向的对象本身是一个常量，使用`const_cast`获得的写权限是非法行为；
```cpp
#include<iostream>

using namespace std;

int main()
{
    // 情况一：合法
	int i = 1; // i不是常量
	const int * pi = &i;
	int * pi2 = const_cast<int *>(pi);
	*pi2 = 100;
	
	// 情况二：不合法：
	int ci = 1; // ci是const
	const int * pi3 = &ci;
	int * pi4 = const_cast<int *>(pi);
	*pi3 = 100; // 报错 
}

```
运行结果：
```
test.cpp:17:9: error: assignment of read-only location ‘* pi3’
  *pi3 = 100; // 报错
         ^~~
```
**结果分析：**
&emsp;&emsp; 可以看到的是，情况一通过了编译，情况二编译时报错了。

#### (5) `const_cast`的使用场景
&emsp;&emsp; 当我们需要通过`const`实参 来调用 一个形参不是`const`的函数时，`const_cast`可以派上用场：
```cpp
void func(int * ptr){
    cout << "*ptr : " << *ptr << endl;
}

int main()
{
    int i = 100;
    const int *p = &i;
    func(p);
}
```
运行结果：
```cpp
test.cpp: In function ‘int main()’:
test.cpp:13:10: error: invalid conversion from ‘const int*’ to ‘int*’ [-fpermissive]
     func(p);
          ^
test.cpp:5:17: note:   initializing argument 1 of ‘void func(int*)’
 void func(int * ptr){
           ~~~~~~^~~
```
**结果分析：**
&emsp;&emsp; 因为`func()`的形参是`int*`，而我们传进去的实参是`const int *`，编译器不支持从`const`到 非`const`的转换，所以报错。
将上面的代码稍作修改：
```cpp
void func(int * ptr){
    cout << "*ptr : " << *ptr << endl;
}

int main()
{
    int i = 100;
    const int *p = &i;
    func(const_cast<int *>(p)); // 修改在此
}
```
最终成功通过编译。**结果分析：**
&emsp;&emsp; 通过`const_cast`，我们将指针`p`显示转换成了`int *`，所以通过了编译。

### 1.4.3 `reinterpret_cast`
#### (1) 作用
&emsp; `reinterpret_cast`t意为 **“重新解释，就是为运算对象的位模式提供较低层次的重新解释*”**，它是C++中最接近于C风格强制类型转换的一个关键字。它让程序员能够将一种对象类型转换为另一种，不管它们是否相关。，顾名思义，举个例子，假设有如下的转换：
```cpp 
int *ip;
char pc = reinterpret_cast<char> (ip) ;
```
我们必须牢记 `pc` 所指的真实对象是一个 `int` 而非字符，如果把 `pc` 当成普通的字符指针使用就可能在运行时发生错误。例如:
```cpp 
string str (pc) ;
```
可能导致异常的运行时行为。
&emsp;&emsp; 使用 `reinterpret_` cast 是非常危险的，用 `pc` 初始化 `str` 的例子很好地证明了这一点。其中的关键问题是类型改变了，但编译器没有给出任何警告或者错误的提示信息。当我们用一个 `int` 的地址初始化 `pc` 时，由于显式地声称这种转换合法，所以编译器不会发出任何警告或错误信息。接下来再使用 `pc` 时就会认定它的值是 `char*` 类型，编译器没法知道它实际存放的是指向 `int` 的指针。最终的结果就是，在上面的例子中虽然用 `pc` 初始化 `str` 没什么实际意义，甚至还可能引发更糟糕的后果，但仅从语法上而言这种操作无可指摘。查找这类问题的原因非常困难，如果将 `ip` 强制转换成 `pc` 的语句和用 `pc` 初始化 `string` 对象的语句分属不同文件就更是如此。

#### (2) 语法格式
&emsp; `reinterpret_cast`
```cpp 
reinterpret_cast<type_id> (expression)
```
参数要求：
> `type_id` : 必须是一个指针、引用、算术类型、函数指针或者成员指针
> `expression`: 
> 

#### (3) 如何安全的使用`reinterpret_cast`？
&emsp;&emsp; `reinterpret_cast`本质上依赖于机器，想要安全的使用`reinterpret_cast`必须对涉及的类型和编译器实现转换的过程都非常了解。

### 1.4.4 `dynamic_cast`
#### (1) 作用

#### (2) 使用

```cpp 

```
参数要求：
> `type_id` : 
> `expression`: 
> 

&emsp;
## 1.5 使用 强制类型转换的原则
&emsp; 强制类型转换干扰了正常的类型检查，因此我们强烈建议程序员避免使用强制类型转换：
> &emsp;&emsp; 这个建议对于 `reinterpret_acst` 尤其适用，因为此类类型转换总是充满了风险。
> &emsp;&emsp; 在有重载函数的上下文中使用 `const_cast` 无可厚非，但是在其他情况下使用 `const_cast` 也就意味着程序存在某种设计缺陷。
> &emsp;&emsp; 其他强制类型转换，比如 `static_cast` 和 `dynamic_cast` 都不应该频繁使用。每次书写了一条强制类型转换语句，都应该反复斟酌能否以其他方式实现相同的目标。
> &emsp;&emsp; 就算实在无法避免，也应该尽量限制类型转换值的作用域，并且记录对相关类型的所有假定，这样可以减少错误发生的机会。
> 

## 参考文献
1. [C++ 四种强制类型转换](https://www.cnblogs.com/Allen-rg/p/6999360.html)
2. [为什么说 C++ 的四种命名类型转换比旧式转换更安全？](https://www.zhihu.com/question/400931816)
3. [C++ RTTI 和四种类型转换](https://zhuanlan.zhihu.com/p/354038152)
4. [深入理解C++中五种强制类型转换的使用场景](https://chowdera.com/2021/08/20210803022838030W.html)