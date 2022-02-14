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
## 1. 当程序`throw`一个异常时，程序的执行流程发生了什么变化？
&emsp;&emsp; 当程序`throw`一个异常时，跟在`throw`的代码将不再被执行，程序的控制权从`throw`转移到与之匹配的`catch`模块。该`catch`模块可能是位于同一函数中的局部`catch`，也可能位于直接或间接调用了发生异常的另一个函数中。
&emsp;&emsp; 控制权从一处转移到了另一处，这有两个重要意义：
> ① 沿着调用链的函数可能会提早退出；
> ② 一旦程序开始执行异常处理代码，则沿着调用链创建的对象将被销毁。
> 


&emsp;
## 2. 异常的匹配过程是怎样的？如果没有匹配上会发生什么？
### 2.1 匹配过程
&emsp;&emsp; 当`throw`一个异常时，异常的匹配过程相当于一个栈展开(Stack Unwinding)的过程:
<div align="center"> <img src="./pic/chapter18/栈展开.png"> </div>

### 2.2 如果没匹配上，将发生什么？
&emsp;&emsp; 如果一个异常没有被捕获，则它将终止当前程序。


&emsp;
## 3. 析构函数 和 异常
### 3.1 析构函数可以抛异常吗？
&emsp;&emsp; C++并不阻止在类的析构函数中抛出异常，但它不鼓励这么做。


&emsp;
## 4. 异常对象(Exception Object)
&emsp;&emsp; 异常对象是一种特殊的对象，编译器使用异常抛出表达式来对异常对象进行拷贝初始化。因此，`throw`语句中的表达式必须拥有完全类型(参见7.3.3节，第250页)。而且如果该表达式是类类型的话，则相应的类必须含有一个可访问的析构函数和一个可访问的拷贝或移动构造函数。如果该表达式是数组类型或函数类型，则表达式将被转换成与之对应的指针类型。
&emsp;&emsp; 异常对象位于由编译器管理的空间中，编译器确保无论最终调用的是哪个`catch`子句都能访问该空间。当异常处理完毕后，异常对象被销毁。
&emsp;&emsp; 
&emsp;&emsp; 当我们抛出一个表达式时，该表达式的静态编译时类型决定了异常对象的类型。比如，如果一个`throw`表达式解引用一个基类指针，而该指针实际指向的是派生类对象，则抛出的对象将被切掉一部分，只有基类的部分被成功抛出。
在在 C++ 中，我们使用 throw 关键字来显式地抛出异常，它的用法为：
```cpp
throw exceptionData;
```
exceptionData 是“异常数据”的意思，它可以包含任意的信息，完全有程序员决定。exceptionData 可以是 int、float、bool 等基本类型，也可以是指针、数组、字符串、结构体、类等聚合类型，请看下面的例子：
```cpp
char str[] = "http://c.biancheng.net";
char *pstr = str;
class Base{};
Base obj;

throw 100;   // int 类型
throw str;   // 数组类型
throw pstr;  // 指针类型
throw obj;   // 对象类型
```


&emsp;
## 5. 捕获异常
### 5.1 多级catch
一个`try` 后面可以跟多个 `catch`：
```cpp
try{
    //可能抛出异常的语句
}catch (exception_type_1 e){
    //处理异常的语句
}catch (exception_type_2 e){
    //处理异常的语句
}
//其他的catch
catch (exception_type_n e){
    //处理异常的语句
}
```
当异常发生时，程序会按照从上到下的顺序，将异常类型和 `catch` 所能接收的类型逐个匹配。一旦找到类型匹配的 `catch` 就停止检索，并将异常交给当前的 `catch` 处理（其他的 `catch` 不会被执行）。如果最终也没有找到匹配的 `catch`，就只能交给系统处理，终止程序的运行。

### 5.2 在搜寻`catch`语句的过程中，匹配规则是怎样的？
&emsp;&emsp; 在搜寻`catch`语句的过程中，我们并不能总是能找到异常的最佳匹配，相反的是，挑选出来的应该是第一个和异常匹配的语句，例如下面的代码：
```cpp
#include <iostream>

using namespace std;

class Base{ };
class Derived: public Base{ };

int main()
{
    try{
        throw Derived();  //抛出自己的异常类型，实际上是创建一个Derived类型的匿名对象
        cout<<"This statement will not be executed."<<endl;
    }catch(int){
        cout<<"Exception type: int"<<endl;
    }catch(Base){  //匹配成功（向上转型）
        cout<<"Exception type: Base"<<endl;
    }catch(Derived){
        cout<<"Exception type: Derived"<<endl;
    }
    return 0;
}
```
运行结果：
```
Exception type: Base
```
**结果分析：**
&emsp;&emsp; 在上面的代码中，最佳匹配应该是`catch(Derived)`，但是却被`catch(Base)`截胡了，因为在匹配过程中是允许子类到父类的类型转换的，因此`catch(Base)`是第一个和异常匹配的`catch`字句。

### 5.3 `catch`语句中的 类型转换
和函数的形参和实参匹配规则相比，异常和`catch`异常声明的匹配规则受到了更多的限制，
> ① 允许从非常量向常量的类型转换，也就是说，一条非常量对象的 `throw` 语句可以匹配一个接受常量引用的 `catch` 语句。
> ② 允许从派生类向基类的类型转换。
> ③ 数组被转换成指向数组（元素）类型的指针，函数被转换成指向该函数类型的指针。
> 
除此之外，包括标准算术类型转换和类类型转换在内，其它规则都不能在匹配`catch`的过程中使用。
```cpp
#include <iostream>

using namespace std;

int main()
{
    try{
        throw 100;  //抛出自己的异常类型，实际上是创建一个Derived类型的匿名对象
        cout<<"This statement will not be executed."<<endl;
    }catch(float){
        cout<<"Exception type: float"<<endl;
    }catch(int){
        cout<<"Exception type: int"<<endl;
    }
        return 0;
}
```
运行结果：
```
Exception type: int
```
**结果分析：**
&emsp;&emsp; 结果也证明了，不会发生`int`到`float`的转换。

### 5.4 在编写`try-catch`时，应该注意什么？
&emsp;&emsp; 前面已经提到，在某些情况下`catch`语句会发生类型转换，因此越是专门的`catch`越是应该放在整个`catch`列表的前端：
① 非常量类型 应该 写在 常量前面，如：
```cpp
try{
    // 略... 
}catch(string){
    // 略...
}catch(const string){
    // 略...
}
```
② 如果多个`catch`语句的类型存在着继承关系，则应该吧继承链最底端的类放在前面，而将继承链最顶端的类放在后面，举个例子，假设有爷爷类`Grandfather`、父类`Father`、子类`Son`类，则应该这么写：
```cpp
try{
    // 略... 
}catch(Son){
    // 略...
}catch(Father){
    // 略...
}catch(Grandfather){
    // 略...
}
```

### 5.5 重新抛出(rethrowing)
#### 5.5.1 为什么会需要重新抛出？
&emsp;&emsp; 有时，一个单独的`catch`语句不能完整的处理某个异常，因此在执行了某些矫正操作之后，当前的`catch`可能会决定由调用链更上一层的函数接着处理。
#### 5.5.2 如何重新抛出？
&emsp;&emsp; 一条 catch 语句通过重新抛出的操作将异常传递给另外一个catch语句。这里的重新抛出仍然是一条throw语句，只不过不包含任何表达式:
```cpp
throw;
```
&emsp;&emsp;空的`throw`语句**只能出现在**`catch` 语句或`catch`语句直接或间接调用的函数之内。如果在处理代码之外的区域遇到了空`throw`语句，编译器将调用 `terminate`。
&emsp;&emsp;一个重新抛出语句并不指定新的表达式，而是将当前的异常对象沿着调用链向上传递。很多时候，`catch` 语句会改变其参数的内容。如果在改变了参数的内容后`catch`语句重新抛出异常，则只有当`catch` 异常声明是引用类型时我们对参数所做的改变才会被保留并继续传播：
```cpp
catch (my_error &eObj) { // specifier is a reference type
    eObj.status = errCodes::severeErr; // modifies the exception object
    throw; // the status member of the exception object is severeErr
} catch (other_error eObj) { // specifier is a nonreference type
    eObj.status = errCodes::badErr; // modifies the local copy only
    throw; // the status member of the exception object is unchanged
}
```

### 5.6 在一个`catch`语句中捕获所有异常
#### 5.6.1 如何捕获所有异常的处理代码？
&emsp;&emsp; 我们可以使用省略号作为异常声明，这样的处理代码被称为捕获所有异常(catch-all)的处理代码，形如：`catch(...)`，它可以和任何类型的异常匹配：
```cpp
#include <iostream>

using namespace std;

int main()
{
    try{
        throw 100;  
        cout<<"This statement will not be executed."<<endl;
    }catch(const char *){
        cout<<"Exception type: const char *"<<endl;
    }catch(...){
        cout<<"Exception type: ..."<<endl;
    }
        return 0;
}
```
运行结果：
```
Exception type: ...
```

#### 5.6.2 使用捕获所有异常(catch-all)的处理代码时要注意什么？
&emsp;&emsp; 如果`catch(...)`和其它的`catch`语句一起出现，则`catch(...)`应该出现在最后，因为出现`catch(...)`后面的`catch`永远不会被捕获。

### 5.7 函数 try 语句块与构造函数
#### 5.7.1 为什么在构造函数体内`try-catch`不能完全解决构造函数可能发生的异常？
&emsp;&emsp; 通常情况下，程序执行的任何时刻都可能发生异常，特别是异常可能发生在处理构造函数初始值的过程中。构造函数在进入其函数体之前先执行初始值列表，若在执行初始值列表时抛出异常，但此时函数体内的`try`语句块还未生效，所以构造函数体内的`catch`语句无法处理构造函数初始值列表抛出的异常。
#### 5.7.2 如何处理构造函数初始值抛出的异常？
&emsp;&emsp; 需要将构造函数写成 函数`try`语句块(函数测试块，function block)的形式。
&emsp;&emsp; 函数`try`语句块使得一组`catch`语句既可以处理构造函数体(或析构函数体)，也能处理构造函数的初始化过程(或析构函数的析构过程)。
&emsp;&emsp; 举个例子来说明函数`try`语句块怎么用：
```cpp
template <typename T>
Blob<T>::Blob(std::initializer_list<T> il) try :
                data(std::make_shared<std::vector<T>>(il)) 
{
    /* empty body */
} catch(const std::bad_alloc &e) { handle_out_of_memory(e); }
```
注意，关键字`try`出现在表示构造函数初始值列表的冒号前面。


&emsp; 
## 6. noexcept 异常说明
### 6.1 `noexcept`的作用是？ 
&emsp;&emsp; 在C++11标准中，可以通过提供`noexcept`说明来指定某个函数不会抛出异常。

### 6.2 如何使用`noexcept`？
其形式是关键字`noexcept`紧跟在函数的参数列表后面，用以标识该函数不会抛出异常：
```cpp
void recoup(int) noexcept;  // won't throw
void alloc(int);            // might throw
```
对于一个函数来说，`noexcept` 说明要么出现在该函数的所有声明语句和定义语句中，要么一次也不出现。该说明应该在函数的尾置返回类型（参见6.3.3节，第206页) 之前。我们也可以在函数指针的声明和定义中指定 `noexcept`。在 `typedef` 或类型别名中则不能出现 `noexcept`。在成员函数中，`noexcept`说明符需要跟在`const` 及引用限定符之后，而在`final`、`override`或虚函数的 `= 0`之前。

### 6.3 函数声明为`noexcept`有何优点？
&emsp; 对于用户及编译器来说，预先知道某个函数不会抛出异常显然大有裨益：
> &emsp;&emsp; 首先，知道函数不会抛出异常有助于简化调用该函数的代码；
> &emsp;&emsp; 其次，如果编译器确认函数不会抛出异常，它就能执行某些特殊的优化操作，而这些优化操作并不适用于可能出错的代码。
> 

### 6.4 若在一个声明为`noexcept`的函数内部`throw`了一个异常，能通过编译吗？
&emsp;&emsp; 可以，因为编译器不会在编译的时候检查`noexcept`说明，实际上，如果一个函数在说明了`noexcept`的同时又包含了`throw`语句或调用给你了其它可能抛出异常的函数，编译器将顺利编译通过，并不会因为这种违反异常说明的情况而报错(当然不排除个别编译器会对这种用法提出警告)：

### 6.5 异常说明的实参
&emsp;&emsp; `noexcept` 说明符接受一个可选的实参，该实参必须能转换为`bool`类型: 如果实参是`true`，则函数不会抛出异常；如果实参是`false`，则函数可能抛出异常:
```cpp
void recoup(int) noexcept (true);	//recoup不会抛出异常
void alloc(int) noexcept (false);	//alloc可能抛出异常
```

### 6.6 `noexcept` 运算符
&emsp;&emsp; `noexcept` 说明符的实参常常与 `noexcept` 运算符（noexcept operator）混合使用。`noexcept`运算符是一个一元运算符，它的返回值是一个 `bool` 类型的右值常量表达式，用于表示给定的表达式是否会抛出异常。和 `sizeof`类似，`noexcept`也不会求其运算对象的值：
```cpp
noexcept(recoup(i)) // 如果recoup不抛出异常则结果为true，否则为false
```
更普通的形式是：
```cpp
noexcept(e);
```
当 `e` 调用的所有函数都做了不抛出说明且`e`本身不含有`throw`语句时，上述表达式为`true`；否则`noexcept(e)` 返回`false`。
我们可以使用`noexcept`运算符得到如下的异常说明：
```cpp
void f() noexcept(noexcept(g())); // f 和 g 的异常说明一致
```
如果函数`g`承诺了不抛异常，则`f`也不会抛异常。

### 6.7 异常说明与指针、虚函数和拷贝控制
尽管
#### 6.7.1 指针
&emsp;&emsp; 函数指针和该指针所指的函数 必须具有一致的异常说明。也就是说，如果我们为某个指针做了不抛出异常的声明，则该指针将只能指向不抛出异常的函数。相反，如果我们显式或隐式地说明了指针可能抛出异常，则该指针可以指向任何函数，即使是承诺了不抛出异常的函数也可以。
```cpp
//  recoup 和 pf1 都承诺不会抛出异常
void recoup(int) noexcept (true);
void (*pf1)(int) noexcept = recoup;

// 错误: alloc 可能抛异常，但是pf1已经说明了自己不抛异常
pf1 = alloc; 

// 正确: recoup 不会抛出异常，pf2可能抛异常，二者之间互不干扰
void (*pf2)(int) = recoup;
```
#### 6.7.2 虚函数
&emsp;&emsp; 若基类的虚函数承诺了它不会抛出异常，则后续派生出来的虚函数也必须做出同样的承诺；
&emsp;&emsp; 相反，如果基类的虚函数允许抛出异常，则派生类的对应函数既可以允许抛出异常，也可以不允许抛出异常。
```cpp
class Base {
public:
    virtual double f1(double) noexcept; // 不抛异常
    virtual int f2() noexcept(false);   // 可能抛异常
    virtual void f3();                  // 可能抛异常
};

class Derived : public Base {
public:
    double f1(double);          // 错误: Base::f1 承诺不抛异常
    int f2() noexcept(false);   // 正确: 和 Base::f2 一致
    void f3() noexcept;         // 正确: Derived::f3做了更严格的限定，这是允许的
};
```

### 6.8 `noexcept`总结
`noexcept`有两层含义：
> ① 当跟在函数参数列表后面时它是异常说明符；
> ② 而当做为`noexcept`异常说明的`bool`实参出现时，它是一个运算符
> 


&emsp; 
## 7. 如何限定函数可以`throw`的异常类型？
&emsp;&emsp; 在早期的C++版本中，有更为详细的异常说明方案，该方案可以指定某个函数可能抛出的异常类型：
```cpp
double func (char param) throw (int);
```
上面的语句声明了一个名为 `func` 的函数，此函数只能抛出 `int` 类型的异常。如果抛出其他类型的异常，try 将无法捕获，只能终止程序。
**需要注意的是，这个特性已经在C++11标准中取消了。**但这并不意味着这个特性就没用了，它可以提供和 `noexcept`一样的功能：
```cpp
void recoup(int) noexcept; // recoup doesn't throw
void recoup(int) throw(); // equivalent declaration
```
上面两条声明语句时等价的，它们都承诺`recoup`函数不会抛异常。


&emsp; 
## 8. 异常类层次
### 8.1 标准库异常类
#### 8.1.1 标准异常及其层次关系
&emsp;&emsp; C++语言本身或者标准库抛出的异常都是 `exception` 的子类，称为标准异常（Standard Exception）定义在 `<exception>` 中。下图展示了 exception 类的继承层次：
<div align="center"> <img src="./pic/chapter18/标准异常.jpg"> </div>

先来看一下 `exception` 类的直接派生类：
| 异常名称            | 说  明                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `logic_error`       | 逻辑错误。                                                                                                                                                                                                |
| `runtime_error`     | 运行时错误。                                                                                                                                                                                              |
| `bad_alloc`         | 使用 new 或 new[ ] 分配内存失败时抛出的异常。                                                                                                                                                             |
| `bad_typeid`        | 使用 typeid 操作一个 NULL 指针，而且该指针是带有虚函数的类，这时抛出 bad_typeid 异常。                                                                                                                    |
| `bad_cast`          | 使用 dynamic_cast 转换失败时抛出的异常。                                                                                                                                                                  |
| `ios_base::failure` | io 过程中出现的异常。                                                                                                                                                                                     |
| `bad_exception`     | 这是个特殊的异常，如果函数的异常列表里声明了 `bad_exception` 异常，当函数内部抛出了异常列表中没有的异常时，如果调用的 `unexpected()` 函数中抛出了异常，不论什么类型，都会被替换为  `bad_exception` 类型。 |

`logic_error` 的派生类：
| 异常名称           | 说  明                                                                                                    |
| ------------------ | --------------------------------------------------------------------------------------------------------- |
| `length_error`     | 试图生成一个超出该类型最大长度的对象时抛出该异常，例如 vector 的 resize 操作。                            |
| `domain_error`     | 参数的值域错误，主要用在数学函数中，例如使用一个负值调用只能操作非负数的函数。                            |
| `out_of_range`     | 超出有效范围。                                                                                            |
| `invalid_argument` | 参数不合适。在标准库中，当利用string对象构造 bitset 时，而 string 中的字符不是 0 或1 的时候，抛出该异常。 |

`runtime_error` 的派生类：
 | 异常名称          | 说  明                           |
 | ----------------- | -------------------------------- |
 | `range_error`     | 计算结果超出了有意义的值域范围。 |
 | `overflow_error`  | 算术计算上溢。                   |
 | `underflow_error` | 算术计算下溢。                   |

#### 8.1.2 标准异常的基类`exception`
&emsp;&emsp; `exception`仅仅定义了拷贝构造函数、拷贝赋值运算符、虚析构函数以及一个名为`what`的虚成员，该成员返回一个`const char*`，该指针指向一个以`null`结尾的字符数组，并且保证不会抛异常。
```cpp
class exception{
public:
    exception () throw();  //构造函数
    exception (const exception&) throw();  //拷贝构造函数
    exception& operator= (const exception&) throw();  //运算符重载
    virtual ~exception() throw();  //虚析构函数
    virtual const char* what() const throw();  //虚函数
}
```

#### 8.1.3 `what`成员
&emsp;&emsp; 首先，`what`是一个虚函数，正如它的名字“what”一样，`what`成员可以粗略地告诉你这是什么异常。不过C++标准并没有规定这个字符串的格式，各个编译器的实现也不同，所以 what() 的返回值仅供参考。

#### 8.1.4 如何捕获所有的的标准异常？
&emsp;&emsp; 标准异常（Standard Exception）都是`exception`的子类，我们可以通过下面的语句来捕获所有的标准异常：
```cpp
try{
    //可能抛出异常的语句
}catch(exception &e){
    //处理异常的语句
}
```
上面之所以使用引用，是为了提高效率。如果不使用引用，就要经历一次对象拷贝（要调用拷贝构造函数）的过程。

### 8.2 定义自己的异常类
&emsp;&emsp; 用户可以通过继承和重载 exception 类来定义新的异常。下面的实例演示了如何使用 `std::exception `类来实现自己的异常：
```cpp
#include <iostream>
#include <exception>
using namespace std;
 
class MyException : public exception
{
public:
    const char * what () const noexcept{
        return "MyException.";
    }
};
 
int main()
{
  try{
        throw MyException();
    }catch(MyException& e){
        std::cout << "MyException caught" << std::endl;
        std::cout << e.what() << std::endl;
    }catch(std::exception& e){
        //其他的标准错误
    }
}
```
运行结果：
```
MyException caught
MyException.
```

## 参考文献
1. [C++异常类型以及多级catch](http://c.biancheng.net/cpp/biancheng/view/3283.html)
2. [C++ 异常处理](https://www.runoob.com/cplusplus/cpp-exceptions-handling.html)
3. https://codeleading.com/article/15605892710/






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
## 1. 多重继承(multiple inheritance)
### 1.1 什么是多重继承？如何使用？
&emsp;&emsp; 多重继承是指多个直接基类中产生派生类的能力。多重继承的派生类继承了所有父类的属性。
&emsp;&emsp; 定义多重继承时，只要在派生列表中包含这些父类即可，但每个基类都需要包含一个可选的访问说明符(若省略则为`private`)：
```cpp
class Bear : public ZooAnimal { /* ... */ };
class Panda : public Bear, public Endangered { /* ... */ };    
```
和单继承一样，多重继承的派生列表也只能包含已经被定义过的类，而且这些类不能是`final`（类被`final`修饰，不能被继承）的。

### 1.2 多重继承的派生类 的结构是怎样的？
&emsp;&emsp; 在多重继承关系中，派生类的对象 包含有 每个基类的子对象，例如对于下面的`Panda`类：
```cpp
class Bear : public ZooAnimal { /* ... */ };
class Panda : public Bear, public Endangered { /* ... */ };  
```
它的对象的概念结构为：
<div align="center"> <img src="./pic/chapter18/Panda对象的概念结构.png"> </div>

### 1.3 多重继承的派生类 的初始化
&emsp;&emsp; 构造一个派生类的对象将同时构造并初始化它的所有基类子对象，和从一个基类进行派生一样，多重继承的派生类也只能初始化它的直接基类：
```cpp
// 显示初始化所有基类
Panda::Panda(std::string name, bool onExhibit)
        : Bear(name, onExhibit, "Panda"),
            Endangered(Endangered::critical) { }

// 隐式地使用Bear的默认构造函数初始化Bear子对象
Panda::Panda()
        : Endangered(Endangered::critical) { }
```
派生类的构造函数初始值列表将实参分别传递给每个直接基类。

### 1.4 多重继承的派生类 的初始化顺序是怎样的？
&emsp;&emsp; 其中基类的构造顺序和派生列表中的基类出现顺序保持一致，而与派生类构造函数初始值列表中基类的顺序无关。一个`Panda`对象按照如下次序进行初始化：
> ① `ZooAnimal` 是层次结构的最终基类，是 `Panda` 的第一个直接基类 `Bear` 的基类，它首先被初始化。
> ② `Bear` 是第一个直接基类，第二个被初始化。
> ③ `Endangered` 是第二个直接基类，第三个被初始化。
> ④ `Panda` 是最后的派生类，最后被初始化。
> 

### 1.5 继承的构造函数 与 多重继承
#### 1.5.1 多重继承中，从父类继承的构造函数可能造成冲突
&emsp;&emsp; 在C++11标准中，派生类可以从一个或多个基类继承其构造函数。但是，从多个基类中继承相同的构造函数（即具有相同形参列表的构造函数）是错误的：
```cpp
#include <iostream>
#include <string>
#include <memory>

using namespace std;

struct Base1 {
    Base1() = default;
    Base1(const std::string&);
    Base1(std::shared_ptr<int>);
};

struct Base2 {
    Base2() = default;
    Base2(const std::string&);
    Base2(int);
};

// error: D1 attempts to inherit D1::D1 (const string&) from both base classes
struct D1: public Base1, public Base2 {
    using Base1::Base1; // inherit constructors from Base1
    using Base2::Base2; // inherit constructors from Base2
};


int main()
{
    D1 d;
}
```
编译时报错：
```
test.cpp:22:18: 错误：‘D1::D1(const string&)’ inherited from ‘Base2’
     using Base2::Base2; // inherit constructors from Base2
                  ^
test.cpp:21:18: 错误：conflicts with version inherited from ‘Base1’
     using Base1::Base1; // inherit constructors from Base1
                  ^
```
**结果分析：**
&emsp;&emsp; 在上面的例子中，`using Base1::Base1`和`using Base2::Base2`分别相当于：
```cpp
D1(const std::string& s) : Base1(s) { }
```
`using Base2::Base2`分别相当于：
```cpp
D1(const std::string& s) : Base2(s) { }
```
显然，它俩冲突了，因为这两个构造函数的形参列表相同，都接收一个`const std::string&`实参。

#### 1.5.2 如何解决呢？
&emsp;&emsp; 如果一个类从它的多个基类中继承了相同的构造函数，则这个类必须为该构造函数定义它自己的版本：
```cpp
#include <iostream>
#include <exception>
#include <string>
#include <memory>
using namespace std;

struct Base1 {
    Base1() = default;
    Base1(const std::string&);
    Base1(std::shared_ptr<int>);
};

struct Base2 {
    Base2() = default;
    Base2(const std::string&);
    Base2(int);
};

struct D1: public Base1, public Base2 {
    using Base1::Base1; // inherit constructors from Base1
    using Base2::Base2; // inherit constructors from Base2
    
    // 必须定义一个接收string的构造函数
    D1(const std::string& s) : Base1(s), Base2(s) {}

    // 一旦D1定义了自己的构造函数，则编译器就不会为D1生成默认构造函数了，
    // 因此我们需要主动要求编译器生成默认构造函数
    D1() =default;
};


int main()
{
    D1 d;
}
```
上面的代码顺利通过编译。

### 1.6 析构函数与多重继承
&emsp;&emsp; 和往常一样，派生类中的析构函数只负责清理派生类本身分配的资源，派生类的成员和所有基类被自动销毁。合成析构函数有一个空函数体。
&emsp;&emsp; 析构函数调用的顺序总是与构造函数运行顺序相反。对于一个`Panda`对象，析构函数的调用顺序是 `~Panda`，`~Endangered`，`~Bear`，`~ZooAnimal`。
```cpp
class Bear : public ZooAnimal { /* ... */ };
class Panda : public Bear, public Endangered { /* ... */ };  
```

### 1.7 多重继承的派生类 的拷贝、移动操作
&emsp;&emsp; 和只有一个基类的继承一样，派生类如果定义了自己的拷贝、移动操作，则应该调用基类中对应的操作来完成基类部分的拷贝(移动)。

### 1.8 类型转换 与 多个基类
#### 1.8.1 能转换吗？
&emsp;&emsp; 在单一继承下，派生类对象 可以绑定到 指向其基类的指针或引用。多重继承也是如此。我们可以令 派生类对象 绑定到 指向其基类的指针或引用。例如：
```cpp
// operations that take references to base classes of type Panda
void print(const Bear&);
void highlight(const Endangered&);
ostream& operator<<(ostream&, const ZooAnimal&);

// ying_yang 是派生类对象
Panda ying_yang("ying_yang");
print(ying_yang); // passes Panda to a reference to Bear
highlight(ying_yang); // passes Panda to a reference to Endangered
cout << ying_yang << endl; // passes Panda to a reference to ZooAnimal
```

#### 1.8.2 在多继承中，编译器如何在 多个可选的类型转换中 做出选择？
&emsp;&emsp; 但需要注意的是，编译器不会在派生类的几种转换中进行比较和选择，因为在它看来转换到任意一种积累都一样好。例如如果存在下面两个`print()`重载形式，则通过`Panda`对象对不带前缀限定符的`print()`进行调用将产生变异性错误：
```cpp
void print(const Bear&);C++ Primer, Fifth Edition
void print(const Endangered&);

Panda ying_yang("ying_yang");
print(ying_yang); // error: ambiguous
```

#### 1.8.3 基于指针类型或引用类型的查找
&emsp;&emsp; 和单继承一样，对象、指针或引用的静态类型 决定了可以使用哪些成员。就拿前面的例子来说：
```cpp
class Bear : public ZooAnimal { /* ... */ };
class Panda : public Bear, public Endangered { /* ... */ };  
```
> ① 如果使用 `ZooAnimal` 指针，则只有定义在`ZooAnimal`中的操作可以使用，`Panda` 接口中的 `Bear`、`Panda` 和 `Endangered`特有的部分 是看不见的；
> ② 类似地，`Bear` 指针或引用 只知道 `Bear` 和 `ZooAnimal` 成员；
> ③ `Endangered` 指针或引用仅限于 `Endangered` 成员。
> 

### 1.9 多重继承下的的类作用域
&emsp;&emsp; 在单一继承下，派生类的作用域嵌套在其直接和间接基类的作用域中。查找是通过向上搜索继承层次结构，直到找到给定的名字。派生类中定义的名字将隐藏基类中的同名成员。
&emsp;&emsp; 在多重继承下，相同的查找过程在所有直接基类中**同时发生**。如果一个名字通过多个基类找到，则该名字的使用是具有二义性的。
&emsp;&emsp; 例如，如果通过 `Panda`的对象、指针或引用 使用了某个名字，则程序会并行的在`Endangered` 和 `Bear`/`ZooAnimal` 这两颗子树中查找。如果在多个子树中找到该名字，则该名字的使用具有二义性。
&emsp;&emsp; 



https://blog.csdn.net/HPP_CSDN/article/details/112780427