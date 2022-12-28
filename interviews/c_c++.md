[toc]






&emsp;
&emsp; 
# 1. C 和 C++ 的区别和联系






&emsp;
&emsp; 
# 2. new 和 malloc
## 2.1 new 和 malloc 有何区别？
**① 最大的区别**
> &emsp;&emsp; `new`在申请空间的时候会调用构造函数，`malloc`不会调用
>
**② 申请成功时的返回类型**
> &emsp;&emsp; `new`操作符申请内存成功时，返回的是对象类型的指针，类型严格与对象匹配，无需进行类型转换，因此new是类型安全性操作符。
> &emsp;&emsp; `malloc`申请内存成功则返回`void*`，需要手动进行强制类型转换为我们所需的类型；
> 
**③ 申请失败的返回类型**
> &emsp;&emsp;` new`在申请空间失败后返回的是错误码`bad_alloc`，`malloc`在申请空间失败后会返回`NULL`
>
**④ 属性上的区别**
> &emsp;&emsp; `new/delete`是C++关键字需要编译器支持，`maollc`是库函数，需要添加头文件
>
**⑤ 调用时所需参数**
> &emsp;&emsp; `new`在申请内存分配时不需要指定内存块大小，编译器会更具类型计算出大小；而`malloc`需要显示的指定所需内存的大小
>
**⑥ 自定义类型**
> &emsp;&emsp; `new`会先调`operator new`函数，申请足够的内存（底层也是`malloc`实现），然后调用类的构造函数，初始化成员变量，最后返回自定义类型指针。`delete`先调用析构函数，然后调用`operator delete`函数来释放内存（底层是通过`free`实现）。
> &emsp;&emsp; `malloc/free`是库函数，只能动态的申请和释放内存，无法强制要求其做自定义类型对象构造和析构函数
> 
**⑦ 重载**
> &emsp;&emsp; C++允许重载`new/delete`操作符，特别的，布局`new`的就不需要为对象分配内存，而是指定了一个地址作为内存起始区域，`new`在这段内存上为对象调用构造函数完成初始化工作，并返回地址。`malloc`不允许重载。
> 

## 2.2 new 的实现原理
&emsp;&emsp; 如果是简单类型，则直接调用 `operator new()`，在 `operator new()`函数中会调用 `malloc()`函数，如果调用 `malloc()` 失败会调用 `_callnewh()`，如果 `_callnewh()` 返回 `0` 则抛出 `bac_alloc` 异常，返回非零则继续分配内存。 
&emsp;&emsp; 如果是复杂类型，先调用 `operator new()`函数，然后在分配的内存上调用构造函数。 



&emsp;
&emsp; 
# 3. `delete` 和 `free` 的区别：
(1) `free` 不会调用对象的析构函数，而 `delete` 会调用对象的析构函数；
(2) `delete` 用于释放 `new` 分配的空间，`free` 有用释放 `malloc` 分配的空间；
(3) `delete` 是操作符，而 `free` 是函数；
(4) 调用 `free` 前要检查要释放的指针是否为 `NULL`，使用 `delete` 释放内存则不需要；





&emsp;
&emsp; 
# 4. malloc 的实现原理是怎样的？






&emsp;
&emsp; 
# 5. 什么是内存泄露，如何检测？如何避免？
## 5.1 什么是内存泄漏？
&emsp;&emsp; 内存泄漏（Memory Leak）是指程序中已动态分配的堆内存由于某种原因未被释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。

## 5.2 如何检测内存泄漏？
&emsp;&emsp; 在每次 动态分配内存和释放动态内存 的时候都写日志，然后后面分析日志看有没有哪次动态分配的内存没有被释放的。
&emsp;&emsp; 或者利用LeakTracer等工具进行检测；


## 5.3 如何避免内存泄漏？
&emsp;&emsp; 正确的使用智能指针





&emsp;
&emsp; 
# 6. C 语言里面 `volatile`，可以和 `const` 同时使用吗?
&emsp;&emsp; 首先，`volatile`限定符是用来告诉计算机，所修饰的变量的值随时都会改变。用于防止编译器对代码的优化，换句话说，就是编译器在用到这个变量的时候都要从内存中重新读取这个变量的值，而不是使用保存在寄存器中的备份。
&emsp;&emsp; 而`const`所修饰的变量的值在代码中不能进行修改，和`volatile`限定符不冲突，可以一起使用
。







&emsp;
&emsp; 
# 7 const 和 `#define`的区别是？
&emsp; &emsp; 见 c++ primer第二章的笔记


