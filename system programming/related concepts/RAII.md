## 1. RAII
### 1.1 什么是 RAII ？
&emsp;&emsp; RAII（Resource Acquisition Is Initialization）中文译为：资源获取即初始化，是C++的一种管理资源、避免泄漏的惯用法。利用的就是C++构造的对象最终会被销毁的原则。RAII的做法是使用一个对象，在其构造时获取对应的资源，在对象生命期内控制对资源的访问，使之始终保持有效，最后在对象析构的时候，释放构造时获取的资源。

### 1.2 RAII 是用来解决什么问题的？（为什么要使用RAII？）
&emsp; 在计算机系统中，资源是数量有限且对系统正常运行具有一定作用的元素。比如：网络套接字、互斥锁、文件句柄和内存等等，它们属于系统资源。由于系统的资源是有限的，所以，我们在编程使用系统资源时，都必须遵循三个步骤：
> &emsp; a.获取资源  
> &emsp; b.使用资源 
> &emsp; c.释放资源
> 
但是资源的销毁往往是程序员经常忘记的一个环节，所以程序界就想如何在程序员中让资源自动销毁呢？c++之父给出了解决问题的方案：RAII，它充分的利用了C++语言局部对象自动销毁的特性来控制资源的生命周期。
综上所述，RAII 是用来管理资源、避免资源泄漏的一种方法。

### 1.3 RAII 原理是怎样的？有没有什么不足？
#### 1.3.1 原理
&emsp;&emsp; RAII 是C++编程中很重要的一项技术。其原理是 在对象析构函数中释放该对象获取的资源，利用栈展开过程栈上对象的析构函数将被自动调用的保证，从而正确地释放先前获取的资源。RAII只有在栈展开正常执行的前提下才能正常工作。函数调用和正常的C++异常处理流程(异常处于try-catch块)都存在栈展开。也就是说，RAII主要通过下面两个来实现：
> 栈展开 : 将对象分配的栈上，这样在即使在发生异常时，编译器也对该对象进行销毁；
> 析构函数：利用局部变量在离开其作用域时会被销毁的特性(调用析构函数)，对资源进行销毁。
> 
总的来说，就是将 对象分配的栈上，利用编译器会自动在 离开作用域（或发生try-catch异常） 时销毁资源(这时对象的析构函数就会被调用，达到了释放资源的目的。)
#### 1.3.2 不足
&emsp;&emsp; 正如前面介绍，RAII依赖栈展开，资源是通过栈展开时自动调用析构函数来释放资源的，所以如果 发生了令栈无法正常展开的异常时，资源就无法正常释放了：
> 系统调用_exit()、std::abort、terminal();
> 非法内存访问；
> 信号终止；
> 内存耗尽；
> 
简单讲就是：除了从main函数返回之外，调用`exit(), abort(), terminate()`都不保证调用栈正常展开，即RAII将失效。话说回来，程序都终止了，栈不展开关系也不大，RAII失效就失效吧。但是，RAII失效意味着RAII并不能保证资源一定被释放。对于进程生存期的资源，如文件描述符(打开文件，socket等)、内存等，即使RAII失效，进程占用的资源也终将得到释放，但对于内核生存期和文件系统生存期的资源，如IPC(信号量、消息队列、共享内存)、文件等，RAII是存在缺陷的。是否有更加可靠的办法来释放系统资源呢？这个需要进一步探索。

### 1.4 RAII的应用
#### 1.4.1 代码及运行结果
&emsp;&emsp; 在Linux环境下经常会使用多线程技术，说到多线程，就得提到互斥锁，互斥锁主要用于互斥，互斥是一种竞争关系，用来保护临界资源一次只被一个线程访问，按照我们前面的分析，我们封装一下POSIX标准的互斥锁：
下面的代码是`mutex.h`:
```cpp
// mutex.h
#include <pthread.h>
#include <cstdlib>
#include <stdio.h>
#include <string.h>
class Mutex {
public:
    Mutex();
    ~Mutex();
    void Lock();
    void Unlock(); 
private:
    pthread_mutex_t mu_;

    // 这里是通过将 拷贝构造函数、拷贝赋值运算符声明为private来阻止拷贝
    // 阻止拷贝，在C++11标准中中应该声明为 =delete
    Mutex(const Mutex&);
    void operator=(const Mutex&);
};
```
下面的代码是`mutex.cpp `:
```cpp
//mutex.cpp 
#include "mutex.h"

// 这是作者封装的一个打印错误的函数，打印到 标准错误stderr
static void PthreadCall(const char* label, int result) {
    if (result != 0) {
        fprintf(stderr, "pthread %s: %s\n", label, strerror(result));
    }
}

// 静态初始值 PTHREAD_MUTEX_INITIALIZER，只能用于对经由 静态分配且携带默认属性 的互斥量进行初始化。
// 因此这里是通过调用 pthread_mutex_init()对互斥量进行 动态初始化（详见 TLPI第30章）
Mutex::Mutex() { PthreadCall("init mutex", pthread_mutex_init(&mu_, NULL)); }

// 对于使用 PTHREAD_MUTEX_INITIALIZER 静态初始化的互斥量，无需调用 pthread_mutex_destroy()。
// 但这里是动态分配的，所以需要 调用pthread_mutex_destroy()来释放资源
Mutex::~Mutex() { PthreadCall("destroy mutex", pthread_mutex_destroy(&mu_)); }

void Mutex::Lock() { PthreadCall("lock", pthread_mutex_lock(&mu_)); }

void Mutex::Unlock() { PthreadCall("unlock", pthread_mutex_unlock(&mu_)); }
```
下面的代码是`mutexlock.hpp`:
```cpp
// mutexlock.hpp
#include "mutex.h"

class  MutexLock {
 public:
    explicit MutexLock(Mutex *mu): mu_(mu)  {
        this->mu_->Lock(); // 注意！构造函数自动加锁！
    }
    ~MutexLock() { this->mu_->Unlock(); } // 析构函数自动解锁！

private:
    Mutex *const mu_;
    // 禁止拷贝
    MutexLock(const MutexLock&);
    void operator=(const MutexLock&);
};
```
下面的代码是`test_mutexlock.cpp `:
```cpp
// test_mutexlock.cpp 
#include "mutexlock.hpp"
#include <unistd.h>
#include <iostream>

#define    NUM_THREADS     10000

int num=0;
Mutex mutex; // Mutex对象只需定义一个，可以重复使用。

void *count(void *args) {
    /* ① 每次进入count()都新建一个 MutexLock对象，
           新建对象时会调用构造函数 MutexLock：MutexLock()，
             它会自动加锁。*/
    MutexLock lock(&mutex);
    num++;
    /* ② 离开 临时变量lock 的作用域，OS会自动调用 MutexLock：~MutexLock()，
            这个析构函数会自动解锁，程序员无需操心*/
}

int main() {
    int t;
    pthread_t thread[NUM_THREADS];

    for( t = 0; t < NUM_THREADS; t++) {   
        int ret = pthread_create(&thread[t], NULL, count, NULL);
        if(ret) {   
            return -1;
        }   
    }

    for( t = 0; t < NUM_THREADS; t++)
        pthread_join(thread[t], NULL);
    std::cout << num << std::endl;
    return 0;
}
```
上面的代码编译后运行，结果为：
```
[jack@localhost mutex]$ g++ test_mutexlock.cpp mutexlock.hpp mutex.cpp mutex.h -o test_mutexlock -lpthread
[jack@localhost mutex]$ ./test_mutexlock 
10000
```
现将`test_mutexlock.cpp`中的`count()`中的`MutexLock lock(&mutex);`注释掉，然后编译运行：
```
9749
```
#### 1.4.2 分析
(1) 首先，封装一个`Mutex`类，它负责定义一个互斥量，并提供加锁和解锁操作。
(2) 然后，在 `MutexLock`类中，实现了RAII：
```cpp
class  MutexLock {
 public:
    explicit MutexLock(Mutex *mu): mu_(mu)/*立即初始化私有变量 MutexLock::mu_ */  {
        this->mu_->Lock(); // 注意！在构造函数自动加锁！
    }
    ~MutexLock() { this->mu_->Unlock(); } // 析构函数自动解锁！
private:
    Mutex *const mu_;
    // ...其它成员略
}
```
① 在成员初始化列表中初始化自己的成员 `MutexLock::mu_`
② 在构造函数中加锁
③ 在析构函数自动解锁！
(3) 再来看 `count()`:
```cpp
void *count(void *args) {
    /* ① 每次进入count()都新建一个 MutexLock对象，
           新建对象时会调用构造函数 MutexLock：MutexLock()，
             它会自动加锁。*/
    MutexLock lock(&mutex);
    num++;
    /* ② 离开 临时变量lock 的作用域，OS会自动调用 MutexLock：~MutexLock()，
            这个析构函数会自动解锁，程序员无需操心*/
}
```
这样，我们就利用RAII思想，避免了忘记释放资源(在这里是解锁)的错误。

#### 1.4.3 其它应用
RAII通常可以用来:
> 关闭文件
> 释放互斥锁
> 释放其他的系统资源。
> 






&emsp;
&emsp;
## 参考文献
1. [C++RAII机制](https://blog.csdn.net/quinta_2018_01_09/article/details/93638251)
2. [c++经验之谈一：RAII原理介绍](https://zhuanlan.zhihu.com/p/34660259)
3. [C++堆，栈，RAII](https://zhuanlan.zhihu.com/p/354611651)
4. [RAII、栈展开和程序终止](https://www.cnblogs.com/zhenjing/archive/2011/07/06/stackunwinding.html)
5. https://www.zhihu.com/question/388878034/answer/1164049863
6. https://blog.csdn.net/noahzuo/article/details/51139940
7. 