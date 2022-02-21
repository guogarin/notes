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
### 1.2.1 旧有的C风格的强制类型转换
&emsp;&emsp; 此方法不推荐，因为此方法在代码中不显眼，容易被忽略， 而且旧式强制转换实际上是困难且容易出错的。
```cpp
int a = 1;
(double) a;
```
### 1.2.2 C++ 提供的强制类型转换
C++ 提供四种转换操作符来实现显式类型转换：
> ① `static_cast`
> ② `const_cast`
> ③ `reinterpret_cast`
> ④ `dynamic_cast`
> 
#### (1)`static_cast`
供四种转换操作符来实现显式类型转换：
```cpp
static_cast <new_type> (expression)
```
需要注意的是，`static_cast`强制转换只会在编译时检查，但没有运行时类型检查来保证转换的安全性。同时，`static_cast`不能转换掉表达式的`const`、`volitale`或者`__unaligned`属性。

主要有如下几种用法：
* 1）用于类层次结构中基类和派生类之间指针或引用的转换
    * 进行上行转换（把派生类的指针或引用转换成基类表示）是安全的
    * 进行下行转换（把基类的指针或引用转换为派生类表示），由于没有动态类型检查，所以是**不安全**的；
* 2）用于基本数据类型之间的转换，如把`int`转换成`char`。这种转换的安全也要开发人员来保证；
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

#### (4)`dynamic_cast`
基本用法：
```cpp 
dynamic_cast<type_id> (expression)
```


#### (3)`const_cast`
#### (4)`reinterpret_cast`

## 参考文献
1. [c++ 四种强制类型转换介绍](https://blog.csdn.net/ydar95/article/details/69822540)
2. [C++ 类型转换](https://blog.csdn.net/shuzfan/article/details/77338366)
3. [C++ 四种强制类型转换](https://www.cnblogs.com/Allen-rg/p/6999360.html)
4. [C++进阶--类型转换](https://www.jianshu.com/p/5cb9800b6697)
5. [为什么说 C++ 的四种命名类型转换比旧式转换更安全？](https://www.zhihu.com/question/400931816)