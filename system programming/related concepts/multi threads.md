- [](#)
  - [1. 多线程程序为什么难？](#1-多线程程序为什么难)
  - [2. 什么是 `happens-before`原则 ？](#2-什么是-happens-before原则-)
  - [3. 什么是`NTPL`？](#3-什么是ntpl)
- [二、关于多线程的一些问题的解答（摘自muduo的书中）](#二关于多线程的一些问题的解答摘自muduo的书中)
  - [1．Linux下能启动的线程数量](#1linux下能启动的线程数量)
    - [1.1 Linux能同时启动多少个线程？](#11-linux能同时启动多少个线程)
    - [1.2 如何增加系统可以创建的线程数量？](#12-如何增加系统可以创建的线程数量)
  - [2．多线程能提高并发度吗？](#2多线程能提高并发度吗)
  - [4 多线程能降低响应时间吗？](#4-多线程能降低响应时间吗)
  - [5．多线程程序如何让IO和“计算”相互重叠，降低latency？](#5多线程程序如何让io和计算相互重叠降低latency)
  - [6．为什么第三方库往往要用自己的线程？](#6为什么第三方库往往要用自己的线程)
  - [7. 什么是线程池大小的阻抗匹配原则？](#7-什么是线程池大小的阻抗匹配原则)
  - [8．除了你推荐的`Reactor＋thread pool`， 还有别的non-trivial多线程编程模型吗？](#8除了你推荐的reactorthread-pool-还有别的non-trivial多线程编程模型吗)
  - [9．模式(2)和模式(3)a该如何取舍？](#9模式2和模式3a该如何取舍)
- [三、关于线程的`bash`命令](#三关于线程的bash命令)
  - [](#-1)
- [参考文献](#参考文献)



&emsp;
# 一、多线程的相关知识
## 1. 编写多线程程序难在哪？
学习多线程编程面临的最大的思维方式的转变有两点：
* (1) 当前线程可能随时会被切换出去， 或者说被抢占（preempt） 了。
* (2) 多线程程序中事件的发生顺序不再有全局统一的先后关系。
&emsp;&emsp; 当线程被切换回来继续执行下一条语句（指令）的时候，全局数据（包括当前进程在操作系统内核中的状态）可能已经被其他线程修改了。例如，在没有为指针`p`加锁的情况下，`if (p && p->next) { /* ... */ }`有可能导致segfault，因为在逻辑与（`&&`）的前一个分支evaluate为`true`之后的一刹那，p可能被其他线程置为`NULL`或是被释放， 后一个分支就访问了非法地址。
&emsp;&emsp; 在单CPU系统中，理论上我们可以通过记录CPU上执行的指令的先后顺序来推演多线程的实际交织（interweaving）运行的情况。在多核系统中，多个线程是并行执行的，我们甚至没有统一的全局时钟来为每个事件编号。在没有适当同步的情况下，多个CPU上运行的多个线程中的事件发生先后顺序是无法确定的。在引入适当同步后，事件之间才有了happens-before关系。
&emsp;&emsp; 多线程系统编程的难点不在于学习线程原语（primitives），而在于理解多线程与现有的C/C++库函数和系统调用的交互关系，以进一步学习如何设计并实现线程安全且高效的程序。


&emsp;
## 2. 下面这段代码有什么问题？
```cpp
bool running = false;    //全局标志

void threadFunc()
{
    while(running){
        //get task from queue
    }
}

void start(){
    muduo::Thread t(threadFunc);
    t.start();
    running = true;    //应该放到t.start()之前。
}
```
**存在的问题：**
&emsp;&emsp; 这段代码暗中假定线程函数的启动慢于`running`变量的赋值，因此线程函数能进入`while`循环执行我们想要的功能。如果上机测试运行这段代码，十有八九会按我们预期的那样工作。 但是，直到有一天，系统负载很高，`Thread::start()`调用`pthread_create()`陷入内核后返回时， 内核决定换另外一个就绪任务来执行。于是running的赋值就推迟了， 这时线程函数就可能不进入`while`循环而直接退出了。
&emsp;&emsp; 或许有人会认为在`while`之前加一小段延时（sleep）就能解决问题，但这是错的，无论加多大的延时，系统都有可能先执行`while`的条件判断，然后再执行`running`的赋值。正确的做法是把`running`的赋值放到`t.start()`之前，这样借助`pthread_create()`的happens-before语意来保证`running`的新值能被线程看到。
**一个原则：**
&emsp;&emsp; 多线程程序的正确性不能依赖于任何一个线程的执行速度， 不能通过原地等待（sleep()） 来假定其他线程的事件已经发生， 而必须通过适当的同步来让当前线程能看到其他线程的事件的结果。无论线程执行得快与慢（被操作系统切换出去得越多，执行越慢），程序都应该能正常工作。例如下面这段代码就有这方面的问题。


&emsp;
## 3. 什么是`NPTL`？
&emsp;&emsp; NPTL(Native POSIX Threads Library)，这是 Linux 线程实现的现代版，Linux 2.6之后的都是NTPL线程。它的前任是`LinuxThreads`，是最初的 Linux 线程实现，先已被`NTPL`取代。
&emsp;&emsp; 设计 NPTL 是为了弥补 `LinuxThreads` 的大部分的缺陷。特别是如下部分：
> NPTL 更接近 SUSv3 Pthreads 标准。
> 使用 NPTL 的有大量线程的应用程序的性能要远优于 LinuxThreads。
> 


&emsp;
## 4. 为什么说 线程安全的函数是不可组合的？
&emsp;&emsp; 尽管单个函数是线程安全的， 但两个或多个函数放到一起就不再安全了。 例如`fseek()`和`fread()`都是安全的， **但是对某个文件“先seek再read”这两步操作中间有可能会被打断， 其他线程有可能趁机修改了文件的当前位置，让程序逻辑无法正确执行**。 在这种情况下， 我们可以用flockfile(FILE*)和funlockfile(FILE*)函数来显式地加锁。 并且由于FILE*的锁是可重入的，加锁之后再调用fread()不会造成死锁。
&emsp;&emsp; 如果程序直接使用`lseek(2)`和`read(2)`这两个系统调用来随机读取文件， 也存在“先seek再read”这种race condition， 但是似乎我们无法高效地对系统调用加锁。 
&emsp;&emsp; 例如，在单线程程序中，如果我们要临时转换时区，可以用txset() 函数，这个函数会改变程序全局的“当前时区”。
```cpp
//获取伦敦的当前时间
string oldTz = getenv("TZ");  //save TZ,assumeing non-NULL
putenv("TZ=Europe/London");  //set TZ to London
tzset();  //load London time zone

struct tm localTimeInLN;
time_t now = time(NULL);  //get time in UTC
localtime_r(&now, &localTimeInLN);  //convert to London local time
setenv("TZ", oldTz.c_str(), 1);  //restore old TZ
tzset();  //local old time zone
```
&emsp;&emsp; 但是在多线程程序中，这么做是不安全的，即便`tzset()`本身是线程安全的。因为他改变了全局状态，这有可能影响其他线程转换当前时间，或者被其他进行类似的操作的线程影响。解决办法是使用`mudou::TimeZone class`，每个immutable instance 对应一个时期，这样时间转换就不需要修改全局状态了。例如：
```cpp
class TimeZone{
public:
    explict TimeZone(const char* zonefile);
    struct tm toLocalTime(time_t secondsSinceEpoch)const;
    time_fromLocalTime(const struct tm&)const;
    // default copy ctor /assigment /dtor are okay.
    // ...
};
const TomeZone kNewYorkTz("/usr /share / zoneinfo / America /New_York");
const TomeZone kLondonTz(" /usr / share / zoneinfo /Europe / London");
time_t now = time(NULL);
struct tm locaTimeInNY= kNewYorkTz.toLcalTime(now);
struct tm localTimeInLN = kLondonTz.toLocalTime (now);
```
&emsp;&emsp; 对于c/c++库的作者来说，符合设计线程安全的接口也成了一大考验，值得效仿的例子并不多。一个基本的思路尽量吧class 设计成immutable 的，这样用起来就不必为线程安全操心了。
&emsp;&emsp; 尽管c++03标准没有明说标准库的线程的安全性，但我们可以遵循一个基本的原则：凡是非共享的对象都是彼此独立的，如果一个对象从始至终只被一个线程用到，那么它就是安全的。


&emsp;
## 5. 什么是 `happens-before`原则 ？
&emsp;&emsp; 


&emsp;
## 6. 多线程的`errno` 和 多线程的`errno` 有何区别？
&emsp;&emsp; 在传统 UNIX API 中， `errno` 是一全局整型变量。然而，这无法满足多线程程序的需要。如果线程调用的函数通过全局 `errno` 返回错误时，会与其他发起函数调用并检查 `errno` 的线程混淆在一起。换言之，这将引发竞争条件（race condition）。因此，在多线程程序中，每个线程都有属于自己的 `errno`。在 Linux 中， 线程特有 `errno` 的实现方式与大多数 UNIX 实现相类似：将 errno 定义为一个宏，可展开为函数调用，该函数返回一个可修改的左值（lvalue），且为每个线程所独有。（因为左值可以修改，多线程程序依然能以 errno=value 的方式对 errno 赋值。）
&emsp;&emsp; 一言以蔽之， errno 机制在保留传统 UNIX API 报错方式的同时，也适应了多线程环境。
> 最初的 POSIX.1 标准沿袭 K&R 的 C 语言用法，允许程序将 errno 声明为 `extern int errno`。SUSv3 却不允许这一做法（这一变化实际发生于 1995 年的 POSIX.1c 标准之中）。如今，需要声明 errno 的程序必须包含`<errno.h>`，以启用对 errno 的线程级实现。
> 
**总结**：
&emsp;&emsp; 为了适配多线程，现在都把`errno`实现为一个宏，每个线程都有自己的`errno`，以避免竞争条件。


&emsp;
## 7. 同一进程内的多个线程 的资源
### 7.1 同一进程内的多个线程共享了哪些资源？
同一进程中的线程还共享一干其他属性，包括进程 ID、打开的文件描述符、信号处置、当前工作目录以及资源限制。
除了全局内存之外，线程还共享了一干其他属性（这些属性对于进程而言是全局性的，
而并非针对某个特定线程），包括以下内容。
> 进程 ID（process ID）和父进程 ID。
> 进程组 ID 与会话 ID（session ID）。
> 控制终端。
> 进程凭证（process credential）（用户 ID 和组 ID ）。
> 打开的文件描述符。
> 由 fcntl()创建的记录锁（record lock）。
> 信号（signal）处置。
> 文件系统的相关信息：文件权限掩码（umask）、当前工作目录和根目录。
> 间隔定时器（setitimer()）和 POSIX 定时器（timer_create()）。
> 系统 V（system V）信号量撤销（undo， semadj）值（47.8 节）。
> 资源限制（resource limit）。
> CPU 时间消耗（由 times()返回）。
> 资源消耗（由 getrusage()返回）。
> nice 值（由 setpriority()和 nice()设置）。
### 7.2 哪些资源时各线程所独有？
> 线程 ID（thread ID， 29.5 节）。
> 信号掩码（signal mask）。
> 线程特有数据（31.3 节）。
> 备选信号栈（sigaltstack()）。
> errno 变量。
> 浮点型（floating-point）环境（见 fenv(3)）。
> 实时调度策略（real-time scheduling policy）和优先级（35.2 节和 35.3 节）。
> CPU 亲和力（affinity， Linux 所特有， 35.4 节将加以描述）。
> 能力（capability， Linux 所特有，第 39 章将加以描述）。
> 栈，本地变量和函数的调用链接（linkage）信息。
> 


&emsp;
## 8. 多线程还是多进程？
将应用程序实现为一组线程还是进程？我们先分析一下多线程、多进程各自的优缺点吧：
**多线程相较于多线程的优点：**
优点：
> &emsp;&emsp; 线程间的数据共享很简单。相形之下，进程间的数据共享需要更多的投入。例如，创建共享内存段或者使用管道 pipe）。
> &emsp;&emsp; 创建线程要快于创建进程。线程间的上下文切换（context-switch），其消耗时间一般也比进程要短。
> 
**多线程相较于多线程的缺点：**
> &emsp;&emsp; 多线程编程时，需要确保调用线程安全（thread-safe）的函数，或者以线程安全的方式来调用函数。多进程应用则无需关注这些。
> &emsp;&emsp; 某个线程中的 bug（例如，通过一个错误的指针来修改内存）可能会危及该进程的所有线程， 因为它们共享着相同的地址空间和其他属性。 相比之下， 进程间的隔离更彻底。
> &emsp;&emsp; 每个线程都在争用宿主进程（host process）中有限的虚拟地址空间。特别是，一旦每个线程栈以及线程特有数据（或线程本地存储）消耗掉进程虚拟地址空间的一部分，则后续线程将无缘使用这些区域。虽然有效地址空间很大（例如，在 x86-32 平台上通常有 3GB），但当进程分配大量线程，亦或线程使用大量内存时，这一因素的限制作用也就突显出来。与之相反，每个进程都可以使用全部的有效虚拟内存，仅受制于实际内存和交换（swap）空间。
> 
**影响选择的还有如下几点。**
> &emsp;&emsp; 在多线程应用中处理信号，需要小心设计。（作为通则，一般建议在多线程程序中避免使用信号。）关于线程与信号。
> &emsp;&emsp; 在多线程应用中，所有线程必须运行同一个程序（尽管可能是位于不同函数中）。对于多进程应用，不同的进程可以运行不同的程序。
> &emsp;&emsp; 除了数据，线程还可以共享某些其他信息（例如，文件描述符、信号处置、当前工作目录，以及用户 ID 和组 ID）。优劣之判，视应用而定。
> 


&emsp;
## 9. 一个拥有多线程的进程的内存模式是怎样的？
&emsp;&emsp; 同一程序中的所有线程均会独立执行相同程序，且共享同一份全局内存区域，其中包括初始化数据段（initialized data）、未初始化数据段（uninitialized data），以及堆内存段（heap segment）：
<div align="center"> <img src="./pic/multi threads/memory model of a process which contained 4 threads.png"> </div>


&emsp;
## 10. 如何编译多线程程序？
在 Linux 平台上， 在编译调用了 Pthreads API 的程序时， 需要设置 `cc -pthread `的编译选项。使用该选项的效果如下。
* 定义_REENTRANT 预处理宏。这会公开对少数可重入（reentrant）函数的声明。
* 程序会与库 libpthread 进行链接（等价于-lpthread）。
> 编译多线程程序时的具体编译选项会因实现及编译器的不同而不同。 其他一些实现（例如 Tru64）使用 cc –pthread，而 Solaris 和 HP-UX 则使用 cc –mt。
> 
比如，在使用`g++`时，应该这样编译：
```shell
g++ -o test.o test.cpp --std=c++11 -pthread
```


&emsp;
## 11. 什么是 主线程(main thread)？
&emsp;&emsp; 启动程序时，产生的进程只有单条线程，称之为初始（initial）或主（main）线程。


&emsp;
## 12. 线程的执行顺序
### 为什么说不能依赖线程的操作顺序？
&emsp;&emsp; 调用 `pthread_create()`后，应用程序无从确定系统接着会调度哪一个线程来使用 CPU 资源（在多处理器系统中，多个线程可能会在不同 CPU 上同时执行）。程序如隐含了对特定调度顺序的依赖，则无疑会产生 竞争条件。
### 如果要对执行顺序有要求，应该怎么做？
&emsp;&emsp; 如果对执行顺序确有强制要求，那么就必须采用第 30 章所描述的同步技术。







&emsp;
&emsp;
&emsp;
# 二、`POSIX threads`库
&emsp;&emsp; `Pthreads`函数有上百个，但是平常用的就那几个，下面介绍一下这些函数。
## 1. 如何创建线程？
函数 `pthread_create()`负责创建一条新线程。
```cpp
#include <pthread.h>
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                    void *(*start)(void *), void *arg);
// Returns 0 on success, or a positive error number on error                    
```
&emsp;&emsp; **参数 thread** 指向 `pthread_t` 类型的缓冲区，在 `pthread_create()`返回前，会在此保存一个该线程的唯一标识。后续的 Pthreads 函数将使用该标识来引用此线程。
&emsp;&emsp; **参数 attr** 是指向 `pthread_attr_t` 对象的指针，该对象指定了新线程的各种属性，如果将 attr 设置为 NULL，那么创建新线程时将使用各种默认属性。 
&emsp;&emsp; **参数start、arg：** 新线程通过调用带有参数 `arg` 的函数 `start` （即 `start(arg)`）而开始执行。
> 将参数 arg 声明为 void*类型，意味着可以将指向任意对象的指针传递给 start()函数。一般情况下， arg 指向一个全局或堆变量，也可将其置为 NULL。如果需要向 start()传递多个参数，可以将 arg 指向一个结构，该结构的各个字段则对应于待传递的参数。通过审慎的类型强制转换， arg 甚至可以传递 int 类型的值。
> 严格说来，对于 `int` 与 `void*`之间相互强制转换的后果， C 语言标准并未加以定义。不过，大部分 C 语言编译器允许这样的操作，并且也能达成预期的目的，即 `int j == (int) ((void*) j)`。`start()`的返回值类型为 `void*`，对其使用方式与参数 `arg` 相同。对后续 `pthread_join()`函数的描述中，将论及对该返回值的使用方式。
> 



&emsp;
## 2 终止线程
### 2.1 如何让主线程退出的情况下，其它线程继续运行？
&emsp;&emsp; 主如果主线程调用了 `pthread_exit()`，而非调用 exit()或是执行 return 语句，那么其他线程将继续运行。
### 2.2 什么情况下会导致进程中的所有线程立即终止？
会导致进程中的所有线程立即终止的情况：
> (1) 任意线程 调用了`exit()`;
> (2) 主线程 在`main`函数中 执行了`return`语句
> 
### 2.3 有哪些方法可以终止线程？
总的来说，可以以如下方式终止线程的运行。
> (1) 线程 `start` 函数执行 return 语句并返回指定值。
> (2) 线程调用 `pthread_exit()`（详见后述）。
> (3) 调用 `pthread_cancel()`取消线程（在 32.1 节）。
> (4) 任意线程调用了 `exit()`，或者主线程执行了 `return` 语句（在 `main()`函数中），都会导致进程中的所有线程立即终止。
> 

### 2.4 线程如何独立退出？（指的是不影响其它线程下退出）
&emsp;&emsp; 每个线程随后可调用 `pthread_exit()`**独立退出。**（如有任一线程调用了 exit()，那么所有线程将立即终止。）

### 2.5 `pthread_exit()`怎么用？
`pthread_exit()`函数将终止调用线程，且其返回值可由另一线程通过调用 `pthread_join()`来获取：
```cpp
include <pthread.h>
void pthread_exit(void *retval);
```
&emsp;&emsp; **参数 retval** 指定了线程的返回值。 `Retval` 所指向的内容不应分配于线程栈中，因为线程终止后，将无法确定线程栈的内容是否有效。（例如，系统可能会立刻将该进程虚拟内存的这片区域重新分配，供一个新的线程栈使用。）出于同样的理由，也不应在线程栈中分配线程 `start` 函数的返回值。
&emsp;&emsp; 如果主线程调用了 `pthread_exit()`，而非调用 `exit()`或是执行 `return` 语句，那么其他线程将继续运行。



&emsp;
## 3 线程ID
&emsp;&emsp; 一个进程内部的每个线程都有一个唯一标识，称为线程ID。
### 3.1 如何获取线程ID？
### 3.1.1 调用`pthread_create()`的线程 如何获取它创建的线程 的 线程ID？
`pthread_create()`有一个参数用来返回其创建的线程 的 线程ID。
### 3.1.2 线程自己 如何获取自己的线程ID？
一个线程可以通过 `pthread_self()`来获取自己的 线程ID：
```cpp
include <pthread.h>
pthread_t pthread_self(void); // Returns the thread ID of the calling thread
```

### 3.2 为什么说不能依赖`pthread_create()`返回的线程ID？
&emsp;&emsp; SUSv3 明确指出，在新线程开始执行之前，实现无需对 thread 参数所指向的缓冲区进行初始化，即新线程可能会在 `pthread_create()`返回给调用者之前已经开始运行。如新线程需要获取自己的线程 ID，则只能使用 `pthread_self()`。

### 3.3 线程ID 有什么用？
线程 ID 在应用程序中非常有用：
* 不同的 Pthreads函数 利用线程ID 来标识要操作的目标线程。这些函数包括 `pthread_join()`、 `pthread_detach()`、`pthread_cancel()`和 `pthread_kill()`等。
* 在一些应用程序中，以特定线程的线程 ID 作为动态数据结构的标签，这颇有用处，既可用来识别某个数据结构的创建者或属主线程，又可以确定随后对该数据结构执行操作的具体线程

### 3.4 如何判断两个线程ID是否相同？
#### 3.4.1 如何比较？
```cpp
include <pthread.h>
// Returns nonzero value if t1 and t2 are equal, otherwise 0
int pthread_equal(pthread_t t1, pthread_t t2); 
```
例如，为了检查 调用线程的线程ID 与保存于变量`t1`中的线程ID 是否一致，可以编写如下代码：
```cpp
if (pthread_equal(tid, pthread_self())
	printf("tid matches self\n");
```
#### 3.4.2 为什么不直接用`==`？
&emsp;&emsp; 因为必须将 `pthread_t` 作为一种不透明的数据类型加以对待， 所以函数 `pthread_equal()`是必须的。 Linux 将 `pthread_t` 定义为无符号长整型（`unsigned long`），但在其他实现中，则有可能是一个指针或结构。
#### 3.4.3 `gettid()` 和 `pthread_self()` 有何不一样？
**区别：** 
&emsp;&emsp; `gettid()` 获取的是内核中线程ID
&emsp;&emsp; `pthread_self` 是posix描述的线程ID。
> &emsp;&emsp; POSIX 线程 ID 与 Linux 专有的系统调用 `gettid()`所返回的线程ID 并不相同。 POSIX 线程 ID 由线程库实现来负责分配和维护。`gettid()`返回的线程 ID 是一个由内核（Kernel）分配的数字，类似于进程 ID（process ID）。虽然在 Linux NPTL 线程实现中，每个 POSIX 线程都对应一个唯一的内核线程 ID，但应用程序一般无需了解内核线程ID（况且，如果程序依赖于这一信息，也将无法移植）。
> 

### 3.5 线程ID 是否存在复用？
&emsp;&emsp; 在 Linux 的线程实现中，线程 ID 在所有进程中都是唯一的。不过在其他实现中则未必如此， SUSv3 特别指出，应用程序若使用线程 ID 来标识其他进程的线程，其可移植性将无法得到保证。此外，在对已终止线程施以 `pthread_join()`，或者在已分离（detached）线程退出后，实现可以复用该线程的线程 ID。



&emsp; 
## 4. 连接（joining）已终止的线程
### 4.1 什么是 连接(joining)？
&emsp;&emsp; 以 <span style="color:red;font-weight:bold">阻塞的方式</span> 等待 `实参thread `指定的线程结束。当函数返回时，被等待线程的资源被收回。如果线程已经结束，那么该函数会立即返回。并且被join的线程必须是`joinable`的。

### 4.2 如何连接(joining)？
&emsp;&emsp; 函数 `pthread_join()`等待由 实参`thread` 标识的线程终止。如果线程已经终止，`pthread_join()`会立即返回）。这种操作被称为连接(joining)。
```cpp
include <pthread.h>

// Returns 0 on success, or a positive error number on error
int pthread_join(pthread_t thread, void **retval);
```
形参讲解：
> **thread** ： 待连接的线程的线程ID；
> **retval** ： 若 `retval` 为一非空指针，将会保存线程终止时返回值的拷贝，该返回值亦即线程调用`return` 或 `pthred_exit()`时所指定的值。
> 

### 4.3 如果对同一线程多次 连接(joining) 会发生什么？
&emsp;&emsp; 如向 `pthread_join()`传入一个之前已然连接过的线程 ID，将会导致无法预知的行为。例如，相同的线程 ID 在参与一次连接后恰好为另一新建线程所重用，再度连接的可能就是这个新线程。

### 4.4 什么样的线程可以被 连接(joining)？ 
&emsp;&emsp; 默认情况下，线程是可连接的(joinable)，也就是说，当线程退出时，其他线程可以通过调用 `pthread_join()`获取其返回状态。
&emsp;&emsp; 有时，程序员并不关心线程的返回状态，只是希望系统在线程终止时能够自动清理并移除之。在这种情况下，可以调用 `pthread_detach()`并向 thread 参数传入指定线程的标识符，将该线程标记为处于分离detached）状态。
&emsp;&emsp; 一旦线程处于分离状态，就不能再使用 `pthread_join()`来获取其状态，也无法使其重返“可连接”状态。

### 4.5 如果未对 线程 连接，会发生什么后果？
&emsp;&emsp; 若线程并未分离（detached），则必须使用 `ptherad_join()`来进行连接。如果未能连接，那么线程终止时将产生僵尸线程，与僵尸进程的概念相类似。除了浪费系统资源以外，僵尸线程若累积过多，应用将再也无法创建新的线程。

### 4.6 `pthread_join()`执行的功能类似于针对进程的 `waitpid()`调用，它们之间有何差别？
有如下区别：
* ① 进程的wait()有明显的层次关系，而线程的join操作是没有的，线程之间的关系是对等的（peers）：
> 进程中的任意线程均可以调用 `pthread_join()`与该进程的任何其他线程连接起来。 例如， 如果线程 A 创建线程 B， 线程 B 再创建线程 C，那么线程 A 可以连接线程 C， 线程 C 也可以连接线程 A。这与进程间的层次关系不同，父进程如果使用 fork()创建了子进程，那么它也是唯一能够对子进程调用 wait()的进程。调用 pthread_create()创建的新线程与发起调用的线程之间，就没有这样的关系。
> 
* ② 只能join指定线程，不能join“任意线程”;
> 对于进程，则可以通过调用 `waitpid(-1, &status, options`做到这一点;
> 而对于线程，必须指定 线程ID；
> 
* ③ 不能以非阻塞的方式进行连接
> 

### 4.7  线程join自己
#### 4.7.1 会有什么后果？
可能会有两种结果（都获得了 SUSv3 的支持）：
> (1) 线程死锁，当试图加入自己时遭到阻塞，
> (2) 或者调用 pthread_join()失败，返回错误为 EDEADLK。
> 
在 Linux 中，会发生后一种行为。
#### 4.7.2 如何避免？
在 `tid` 中给定一个线程ID，可使用如下代码来阻止这种不测事件：
```cpp
// 先判断要join的是不是自己，然后根据结果看是否join
if (!pthread_equal(tid, pthread_self()))
	pthread_join(tid, NULL);
```



&emsp; 
## 5 线程的分离(detach)
### 5.1 线程分离 有什么作用？
&emsp;&emsp; 我们都知道，线程若不被join，则将变成僵尸线程；
&emsp;&emsp; 若不希望不想调用join，又不想该线程最后变成僵尸进程，则需要调用`pthread_detach()`函数将该线程标记为 分离状态，处于分离状态的线程将在其终止时被OS自动清理，避免成为僵尸线程。
&emsp;&emsp; **总结**：线程分离可以理解为偷懒，因为处于分离状态的线程不需要join，OS会自动回收它，避免成为僵尸线程。

### 5.2 如何分离一个线程？
&emsp;&emsp; 有时，程序员并不关心线程的返回状态，只是希望系统在线程终止时能够自动清理并移除之。在这种情况下，可以调用 `pthread_detach()`并向 thread 参数传入指定线程的标识符，将该线程标记为处于分离（detached）状态：
```cpp
#include <pthread.h>
// Returns 0 on success, or a positive error number on error
int pthread_detach(pthread_t thread);
```

### 5.3 一个线程如何分离自己？
```cpp
pthread_detach(pthread_self());
```

### 5.4 分离状态 是否可逆？
&emsp;&emsp; 不可逆，一旦线程处于分离状态，就不能再使用 `pthread_join()`来获取其状态，也无法使其重返“可连接”状态。

### 5.5 线程分离状后，是否意味着就和同一进程的其它线程没有关系了？
&emsp;&emsp; 不是。其他线程调用了 `exit()`，或是主线程执行 `return` 语句时，即便遭到分离的线程也还是会受到影响。此时，不管线程处于可连接状态还是已分离状态，进程的所有线程会立即终止。
&emsp;&emsp; 换而言之，`pthread_detach()`只是控制线程终止之后所发生的事情，而非何时或如何终止线程。



&emsp;
## 6. 僵尸线程
### 6.1 僵尸线程是如何产生的？
&emsp;&emsp; 若线程并未分离（detached），则必须使用 `ptherad_join()`来进行连接。如果未能连接，那么线程终止时将产生僵尸线程，与僵尸进程的概念相类似。
### 6.2 僵尸线程的危害
&emsp;&emsp; 除了浪费系统资源以外，僵尸线程若累积过多，应用将再也无法创建新的线程。



&emsp;
## 7. 一个`Pthreads`的使用实例：连接任意已终止线程
### 7.1 原理
&emsp;&emsp; 前面已然提及，使用 `pthread_join()`只能连接一个指定线程。且该函数也未提供任何机制去连接任意的已终止线程。
&emsp;&emsp; 把所有线程放到一个数组里，当有一个线程终止的时候会通过条件变量发信号给主函数里的`pthread_cond_wait()`函数，主线程苏醒后遍历线程数组，检查有哪个线程的状态是 `TS_TERMINATED` ，然后将其join，然后回去继续 wait，直到所有线程均已join。因此，我们需要做如下几件事：
> (1) 需要定义一个 struct ，其中包含tid，线程状态都能；
> (2) 其它就和生产者和消费者中使用pthread_cond_wait()函数差不多；
> 
```cpp
#include <pthread.h>
#include "tlpi_hdr.h"

static pthread_cond_t threadDied = PTHREAD_COND_INITIALIZER;
static pthread_mutex_t threadMutex = PTHREAD_MUTEX_INITIALIZER;
				/* Protects all of the following global variables */

static int totThreads = 0; /* Total number of threads created */
static int numLive = 0; /* Total number of threads still alive or
						   terminated but not yet joined */

static int numUnjoined = 0; /* Number of terminated threads that
								have not yet been joined */

enum tstate {		 /* Thread states */
	TS_ALIVE, 		 /* Thread is alive */
	TS_TERMINATED, 	 /* Thread terminated, not yet joined */
	TS_JOINED 		 /* Thread terminated, and joined */
};

static struct { 		/* Info about each thread */
	pthread_t tid; 		/* ID of this thread */
	enum tstate state;  /* Thread state (TS_* constants above) */
	int sleepTime; 		/* Number seconds to live before terminating */
} *thread;

static void * 			/* Start function for thread */
threadFunc(void *arg)
{
	int idx = *((int *) arg);
	int s;
	sleep(thread[idx].sleepTime); /* Simulate doing some work */
	printf("Thread %d terminating\n", idx);

	s = pthread_mutex_lock(&threadMutex);
	if (s != 0)
		errExitEN(s, "pthread_mutex_lock");

	numUnjoined++;
	thread[idx].state = TS_TERMINATED;

	s = pthread_mutex_unlock(&threadMutex);
	if (s != 0)
		errExitEN(s, "pthread_mutex_unlock");
	s = pthread_cond_signal(&threadDied);
	if (s != 0)
		errExitEN(s, "pthread_cond_signal");

	return NULL;
}

int
main(int argc, char *argv[])
{
	int s, idx;
	if (argc < 2 || strcmp(argv[1], "--help") == 0)
		usageErr("%s nsecs...\n", argv[0]);
		
	thread = calloc(argc - 1, sizeof(*thread));
	if (thread == NULL)
		errExit("calloc");

	/* Create all threads */

	for (idx = 0; idx < argc - 1; idx++) {
		thread[idx].sleepTime = getInt(argv[idx + 1], GN_NONNEG, NULL);
		thread[idx].state = TS_ALIVE;
		s = pthread_create(&thread[idx].tid, NULL, threadFunc, &idx);
		if (s != 0)
			errExitEN(s, "pthread_create");
	}

	totThreads = argc - 1;
	numLive = totThreads;

	/* Join with terminated threads */

	while (numLive > 0) {
		s = pthread_mutex_lock(&threadMutex);
		if (s != 0)
			errExitEN(s, "pthread_mutex_lock");
		while (numUnjoined == 0) {
			s = pthread_cond_wait(&threadDied, &threadMutex);
			if (s != 0)
				errExitEN(s, "pthread_cond_wait");
		}
		for (idx = 0; idx < totThreads; idx++) {
			if (thread[idx].state == TS_TERMINATED){
				s = pthread_join(thread[idx].tid, NULL);
				if (s != 0)
					errExitEN(s, "pthread_join");

				thread[idx].state = TS_JOINED;
				numLive--;
				numUnjoined--;

				printf("Reaped thread %d (numLive=%d)\n", idx, numLive);
			}
		}
		s = pthread_mutex_unlock(&threadMutex);
		if (s != 0)
			errExitEN(s, "pthread_mutex_unlock");
	}
	exit(EXIT_SUCCESS);
}
```
&emsp;&emsp; 

### 1.7
```cpp

```
&emsp;&emsp; 




## 一些练习题
### 若一线程执行了如下代码，可能会产生什么结果？
```cpp
pthread_join(pthread_self(), NULL);
```
在 Linux 上编写一个程序，观察一下实际会发生什么情况。假设代码中有一变量 `tid`，其中包含了某个线程 ID，在自身发起 `pthread_join(tid, NULL)`调用时，要避免造成
**解答**：
可能会有两种结果（都获得了 SUSv3 的支持）：
> (1) 线程死锁，当试图加入自己时遭到阻塞，
> (2) 或者调用 pthread_join()失败，返回错误为 EDEADLK。
> 
在 Linux 中，会发生后一种行为。在 tid 中给定一个线程 ID，可使用如下代码来阻止这种不测事件。
```cpp
if (!pthread_equal(tid, pthread_self()))
	pthread_join(tid, NULL);
```
### 除了缺少错误检查，以及对各种变量和结构的声明外，下列程序还有什么问题？
```cpp
static void * threadFunc(void *arg){
	struct someStruct *pbuf = (struct someStruct *) arg;
	/* Do some work with structure pointed to by 'pbuf' */
}
int main(int argc, char *argv[]){
	struct someStruct buf;
	pthread_create(&thr, NULL, threadFunc, (void *) &buf);
	pthread_exit(NULL);
}
```
**解答：**
&emsp;&emsp; 没有在主线程中对子线程进行join，而是在创建一个线程之后就直接用了pthread_exit()退出了，问题是若主线程调用了pthread_exit()， 那么其他线程将继续运行，因此该程序在主线程终止后， threadFunc()函数继续对主线程堆栈中的数据进行操作，结果难以预测。

&emsp;
&emsp;
&emsp;
# 三、关于多线程的一些问题的解答（摘自muduo的书中）
## 1．Linux下能启动的线程数量
### 1.1 Linux能同时启动多少个线程？
系统能创建的线程数量受下面三个影响：
* ① `/usr/include/bits/local_lim.h`中的PTHREAD_THREADS_MAX限制了进程的最大线程数
* ② `/proc/sys/kernel/threads-max`中限制了系统的最大线程数
* ③ 内存的大小，因为一个线程的默认栈为`8Mb`或`10Mb`，从内存大小的的角度来看，系统支持的最大线程数量为：`内存大小 / 线程默认栈大小`

&emsp;&emsp; 因此，**对于32位的系统**，最多支持`4GiB`内存，其中用户态能访问`3GiB`左右， 而一个线程的默认栈（stack）大小是10MB，心算可知，一个进程大约最多能同时启动300个线程。 如果不改线程的调用栈大小的话， 300左右是上限， 因为程序的其他部分（数据段、代码段、堆、动态库等等）同样要占用内存（地址空间）。
&emsp;&emsp; **对于64位系统**，线程数目可大大增加，根据系统的配置不同，支持的数量也不一样。

### 1.2 如何增加系统可以创建的线程数量？
前面已经提到，一个系统能开启的线程数量受操作系统、内存大小的限制，因此可以这么改：
> 修改系统限制
> 使用u`limit -s`来设置stack size,设置的小一点开辟的线程就多，或者直接加内存。
> 

## 2．多线程能提高并发度吗？
&emsp;&emsp; 如果指的是“并发连接数”，则不能。
&emsp;&emsp; 由问题1可知，假如单纯采用`thread per connection`的模型， 那么并发连接数最多300(32位OS)，这远远低于基于事件的单线程程序所能轻松达到的并发连接数（几千乃至上万，甚至几万）。所谓“基于事件”，指的是用`IO multiplexing event loop`的编程模型，又称`Reactor`模式，在前文中已有介绍。
&emsp;&emsp; 那么采用前文中推荐的one loop per thread呢？至少不逊于单线程程序。实际上单个event loop处理1万个并发长连接并不罕见，一个multi-loop的多线程程序应该能轻松支持5万并发链接。
&emsp;&emsp; **小结**：`thread per connection`不适合高并发场合，其`scalability`不佳。`one loop per thread`的并发度足够大，且与CPU数目成正比。

## 4 多线程能降低响应时间吗？
&emsp;&emsp; 如果设计合理，充分利用多核资源的话，可以。在突发（burst）请求时效果尤为明显。来看下面几个例子：
<span style="color:green; font-size:18px; font-weight:bold"> 例1:多线程处理输入 以memcached服务端为例。 </span>

`memcached`一次请求响应大概可以分为3步：
> (1) 读取并解析客户端输入；
> (2) 操作hashtable；
> (3) 返回客户端。
> 
&emsp;&emsp; 在单线程模式下，这3步是串行执行的。在启用多线程模式时，它会启用多个输入线程（默认是4个） ，并在建立连接时按round-robin法把新连接分派给其中一个输入线程，这正好是我说的one loop per thread模型。这样一来，第(1)步的操作就能多线程并行，在多核机器上提高多用户的响应速度。 第(2)步用了全
局锁， 还是单线程的， 这可算是一个值得继续改进的地方。
&emsp;&emsp; 比如，有两个用户同时发出了请求，这两个用户的连接正好分配在两个IO线程上， 那么两个请求的第(1)步操作可以在两个线程上并行执行，然后汇总到第(2)步串行执行，这样总的响应时间比完全串行执行要短一些（在“读取并解析”所占的比重较大的时候，效果更为明显）。
<span style="color:green; font-size:18px; font-weight:bold"> 例2:多线程分担负载 </span>

&emsp;&emsp; 假设我们要做一个求解Sudoku的服务，这个服务程序在9981端口接受请求，输入为一行81个数字（待填数字用0表示），输出为填好之后的81个数字（1～9），如果无解，输出“NO\r\n”。
&emsp;&emsp; 由于输入格式很简单， 用单个线程做IO就行了。 先假设每次求解的计算用时为10ms， 用前面的方法计算， 单线程程序能达到的吞吐量上限为100qps； 在8核机器上， 如果用线程池来做计算， 能达到的吞吐量上限为800qps。 下面我们看看多线程如何降低响应时间。
假设1个用户在极短的时间内发出了10个请求，我们来看看单线程和多线程的差异：
> &emsp;&emsp; **① 如果用单线程“来一个处理一个”的模型**， 这些reqs会排在队列里依次处理（这个队列是操作系统的TCP缓冲区，不是程序里自己的任务队列）。在不考虑网络延迟的情况下，第1个请求的响应时间是10ms；第2个请求要等第1个算完了才能获得CPU资源，它等了10ms，算了10ms，响应时间是20ms；依此类推，第10个请求的响应时间为100ms；这10个请求的平均响应时间为55ms。
&emsp;&emsp; 如果Sudoku服务在每个请求到达时开始计时，会发现每个请求都是10ms响应时间；而从用户的观点来看，10个请求的平均响应时间为55ms，请读者想想为什么会有这个差异。
> &emsp;&emsp; **② 下面改用多线程：1个IO线程，8个计算线程（线程池**）。二者之间用BlockingQueue沟通。 同样是10个并发请求， 第1个请求被分配到计算线程1，第2个请求被分配到计算线程2，依此类推，直到第8个请求被第8个计算线程承担。第9和第10号请求会等在BlockingQueue里，直到有计算线程回到空闲状态其才能被处理。（请注意， 这里的分配实际上由操作系统来做，操作系统会从处于waiting状态的线程里挑一个，不一定是round-robin的。）这样一来，前8个请求的响应时间差不多都是10ms，后2个请求属于第二批， 其响应时间大约会是20ms， 总的平均响应时间是12ms。 可以看出这比单线程快了不少。
> 
&emsp;&emsp; 由于每道Sudoku题目的难度不一，对于简单的题目，可能1ms就能算出来，复杂的题目最多用10ms。那么线程池方案的优势就更明显， 它能有效地降低“简单任务被复杂任务压住”的出现概率。

## 5．多线程程序如何让IO和“计算”相互重叠，降低latency？
&emsp;&emsp; <span style="color:red; font-size:18px; font-weight:bold"> 基本思路是， 把IO操作（通常是写操作） 通过BlockingQueue交给别的线程去做， 自己不必等待。</span>下面来看几个例子：
<span style="color:green; font-size:18px; font-weight:bold"> 例1：日志（logging） </span>

在多线程服务器程序中，日志（logging）至关重要，本例仅考虑写log file的情况， 不考虑log server。在一次请求响应中， 可能要写多条日志消息， 而如果用同步的方式写文件（fprintf或fwrite） ， 多半会降低性能， 因为：
> 文件操作一般比较慢，服务线程会等在IO上，让CPU闲置，增加响应时间。
> 就算有buffer，还是不灵。多个线程一起写，为了不至于把buffer写错乱，往往要加锁。这会让服务线程互相等待，降低并发度。（同时用多个log文件不是办法，除非你有多个磁盘，且保证log files分散在不同的磁盘上， 否则还是要受到磁盘IO瓶颈的制约。）
> 
&emsp;&emsp; 解决办法是单独用一个logging线程，负责写磁盘文件，通过一个或多个BlockingQueue对外提供接口。别的线程要写日志的时候，先把消息（字符串）准备好，然后往queue里一塞就行，基本不用等待。这样服务线程的计算就和logging线程的磁盘IO相互重叠，降低了服务线程的响应时间。
&emsp;&emsp; 尽管logging很重要，但它不是程序的主要逻辑， 因此对程序的结构影响越小越好，最好能简单到如同一条`printf`语句，且不用担心其他性能开销。而一个好的多线程异步logging库能帮我们做到这一点，见第5
章。（Apache的log4cxx和log4j都支持AsyncAppender这种异步logging方式。）

<span style="color:green; font-size:18px; font-weight:bold"> 例2：memcached客户端 </span>
&emsp;&emsp; 假设我们用memcached来保存用户最后发帖的时间，那么每次响应用户发帖的请求时，程序里要去设置一下memcached里的值。这一步如果用同步IO，会增加延迟。
&emsp;&emsp; 对于“设置一个值”这样的write-only idempotent操作，我们其实不用等memcached返回操作结果，这里也不用在乎set操作失败，那么可以借助多线程来降低响应延迟。比方说我们可以写一个多线程版的memcached的客户端， 对于set操作，调用方只要把key和value准备好，调用一下asyncSet()函数，把数据往BlockingQueue上一放就能立即返回， 延迟很小。剩下的事就留给memcached客户端的线程去操心， 而服务线程不受阻碍。
&emsp;&emsp; 其实所有的网络写操作都可以这么异步地做，不过这也有一个缺点，那就是每次asyncWrite()都要在线程间传递数据。其实如果TCP缓冲区是空的，我们就可以在本线程写完，不用劳烦专门的IO线程。Netty就使用了这个办法来进一步降低延迟。

## 6．为什么第三方库往往要用自己的线程？
&emsp;&emsp; event loop模型没有标准实现。如果自己写代码，尽可以按所用`Reactor`的推荐方式来编程。但是第三方库不一定能很好地适应并融入这个event loop framework，有时需要用线程来做一些串并转换。比方说检测串口上的数据到达可以用文件描述符的可读事件，因此可以方便地融入event loop。但是检测串口上的某些控制信号（例如DCD）只能用轮询（`ioctl(fd, TIOCMGET, &flags)`）或阻塞等待（`ioctl(fd, TIOCMIWAIT, TIOCM_CAR)`）；要想融入event loop，需要单独起一个线程来查询串口信号翻转，再转换为文件描述符的读写事件（可以通过`pipe()`） 。
&emsp;&emsp; 对于Java，这个问题还好办一些，因为`thread pool`在Java里有标准实现，叫`ExecutorService`。如果第三方库支持线程池，那么它可以和主程序共享一个`ExecutorService`，而不是自己创建一堆线程。（比如在初始化时传入主程序的obj。） 对于C++，情况麻烦得多，`Reactor`和`thread pool`都没有标准库。

<span style="color:green; font-size:18px; font-weight:bold"> 例1：libmemcached只支持同步操作 </span>

&emsp;&emsp; `libmemcached`支持所谓的“非阻塞操作”，但没有暴露一个能被`select/poll/epoll`的文件描述符，它的`memcached_fetch`始终会阻塞。它号称`memcached_set`可以是非阻塞的，实际意思是不必等待结果返回，但实际上这个函数会阻塞地调用`write()`，仍可能阻塞在网络IO上。
&emsp;&emsp; 如果在我们的`Reactor event handler`里调用了`libmemcached`的函数， 那么latency就堪忧了。 如果想继续用`libmemcached`， 我们可以为它做一次线程封装， 按问题5中例2的办法，同额外的线程专门做`memcached`的IO， 而程序主体还是Reactor。 甚至可以把`memcached`的“数据就绪”作为一个`event`， 注入我们的`event loop`中， 以进一步提高并发度。 （例子留待问题8讲。）
&emsp;&emsp; 万幸的是，`memcached`的协议非常简单， 大不了可以自己写一个基于`Reactor`的客户端， 但是数据库客户端就没那么幸运了。

<span style="color:green; font-size:18px; font-weight:bold"> 例2： MySQL的官方C API不支持异步操作 </span>

&emsp;&emsp; `MySQL`的官方客户端只支持同步操作，对于`UPDATE/INSERT/DELETE`之类只要行为不管结果的操作（如果代码需要得知其执行结果，则另当别论），我们可以用一个单独的线程来做，以降低服务线程的延迟。可仿照前面`memcached_set`的例子，不再赘言。麻烦的是`SELECT`，如果要把它也异步化，就得动用更复杂的模式了，见问题8。
&emsp;&emsp; 相比之下，`PostgreSQL`的C客户端`libpq`的设计要好得多，我们可以用`PQsendQuery()`来发起一次查询，然后用标准的`select/poll/epoll`来等待`PQsocket`。如果有数据可读，那么用`PQconsumeInput`处理之， 并用`PQisBusy`判断查询结果是否已就绪。最后用`PQgetResult`来获取结果。借助这套异步API， 我们可以很容易地为`libpq`写一套`wrapper`， 使之融入程序所用的`event loop`模型中。

## 7. 什么是线程池大小的阻抗匹配原则？
&emsp;&emsp; 程序里具体用几个loop、线程池的大小等参数需要根据应用来设定，基本的原则是“**阻抗匹配**”，使得CPU和IO都能高效地运作。
&emsp;&emsp; 如果池中线程在执行任务时，密集计算所占的时间比重为`P（0＜P≤1）`，而系统一共有`C`个CPU，为了让这`C`个CPU跑满而又不过载，线程池大小的经验公式`T＝C/P`。`T`是个hint，考虑到`P`值的估计不是很准确，`T`的最佳值可以上下浮动50％.这个经验公式的原理很简单，`T`个线程，每个线程占用`P`的CPU时间， 如果刚好占满`C`个CPU，那么必有`T×P＝C`。下面验证一下边界条件的正确性。
&emsp;&emsp; 假设`C＝8， P＝1.0`，线程池的任务完全是密集计算，那么`T＝8`。只要8个活动线程就能让8个CPU饱和，再多也没用，因为CPU资源已经耗光了。
&emsp;&emsp; 假设`C＝8， P＝0.5`，线程池的任务有一半是计算，有一半等在IO上，那么`T＝16`。考虑操作系统能灵活、合理地调度sleeping/writing/running线程，那么大概16个“50％繁忙的线程”能让8个CPU忙个不停。启动更多的线程并不能提高吞吐量，反而因为增加上下文切换的开销而降低性能。
&emsp;&emsp; 如果`P＜0.2`，这个公式就不适用了，`T`可以取一个固定值，比如`5×C`。另外，公式里的`C`不一定是CPU总数，可以是“分配给这项任务的CPU数目”，比如在8核机器上分出4个核来做一项任务，那么`C＝4`。
&emsp;&emsp; 此外，程序里或许还有个别执行特殊任务的线程，比如logging，这对应用程序来说基本是不可见的，但是在分配资源（CPU和IO）的时候要算进去，以免高估了系统的容量。

## 8．除了你推荐的`Reactor＋thread pool`， 还有别的non-trivial多线程编程模型吗？
&emsp;&emsp; 有，`Proactor`。如果一次请求响应中要和别的进程打多次交道，那么Proactor模型往往能做到更高的并发度。当然，代价是代码变得支离破碎，难以理解。
这里举`HTTP proxy`为例， 一次`HTTP proxy`的请求如果没有命中本地cache，那么它多半会：
> (1) 解析域名（不要小看这一步，对于一个陌生的域名，解析可能要花几秒的时间）；
> (2) 建立连接；
> (3) 发送HTTP请求；
> (4) 等待对方回应；
> (5) 把结果返回给客户。
>
这5步中跟2个server发生了3次round-trip， 每次都可能花几百毫秒：
> (1) 向DNS问域名，等待回复；
> (2) 向对方的HTTP服务器发起连接，等待TCP三路握手完成；
> (3) 向对方发送HTTP request，等待对方response。
> 
&emsp;&emsp; 而实际上`HTTP proxy`本身的运算量不大，如果用线程池，池中线程的数目会很庞大，不利于操作系统的管理调度。这时我们有两个解决思路：
* 思路1： 把“域名已解析”、“连接已建立”、“对方已完成响应”做成event，继续按照Reactor的方式来编程。这样一来，每次客户请求就不能用一个函数从头到尾执行完成，而要分成多个阶段，并且要管理好请求的状态（“目前到了第几步？”）。
* 思路2：用回调函数，让系统来把任务串起来。比如收到用户请求，如果没有命中本地缓存，那么需要执行：
> a．立刻发起异步的DNS解析`startDNSResolve()`， 告诉系统在解析完之后调用`DNSResolved()`函数；
> b．在`DNSResolved()`中，发起TCP连接请求，告诉系统在连接建立之后调用`connectionEstablished()`；
> c．在`connectionEstablished()`中发送`HTTP request`，告诉系统在收到响应之后调用`httpResponsed()`；
> d．最后，在`httpResponsed()`里把结果返回给客户。
> 
&emsp;&emsp; NET大量采用的`BeginInvoke/EndInvoke`操作也是这个编程模式。当然，对于不熟悉这种编程方式的人，代码会显得很难看。有关`Proactor`模式的例子可参看`Boost.Asio`的文档，这里不再多说。
&emsp;&emsp; Proactor模式依赖操作系统或库来高效地调度这些子任务，每个子任务都不会阻塞，因此能用比较少的线程达到很高的IO并发度。
&emsp;&emsp; Proactor能提高吞吐，但不能降低延迟，所以我没有深入研究。另外，在没有语言直接支持的情况下，Proactor模式让代码非常破碎，在C++中使用Proactor是很痛苦的。因此最好在“线程”很廉价的语言中使用
这种方式，这时runtime往往会屏蔽细节，程序用单线程阻塞IO的方式来处理TCP连接。

## 9．模式(2)和模式(3)a该如何取舍？
先介绍一下背景，问题中说的几个模式指的是：
> (1) 运行 一个 单线程的进程；（启动一个进程A，该进程只含有一个线程）
> (2) 运行 一个 多线程的进程；（启动一个进程A，该进程中包含多个线程）
> (3) 运行 多个 单线程的进程；(启动多个进程，A B C...，这些进程各自都只包含一个线程)
> &emsp;&emsp; a 简单地把模式(1)中的进程运行多份 
> &emsp;&emsp; b 主进程+woker进程，如果必须绑定到一个TCP port，比如httpd+fastcgi
> (4) 运行 多个 多线程的进程。(启动多个进程，A B C...，而且这些进程都包含多个线程)
> 
&emsp;&emsp; 作者认为，在其他条件相同的情况下，可以根据工作集（work set 的大小来取舍。工作集是指服务程序响应一次请求所访问的内存大小：
* 如果工作集较大，那么就用多线程，避免`CPU cache`换入换出，影响性能；（指的是避免进程切换时造成的开销） 
* 否则，就用单线程多进程，享受单线程编程的便利。 
举例来说：
> &emsp;&emsp; 如果程序有一个较大的本地cache，用于缓存一些基础参考数据（in-memory look-up table），几乎每次请求都会访问cache，那么多线程更适合一些，因为可以避免每个进程都自己保留一份cache，增加内存使用。
> &emsp;&emsp; `memcached`这个内存消耗大户 用多线程服务端 就比 在同一台机器上运行多个`memcached instance`要好。（但是如果你在16GiB内存的机器上运行32-bit memcached，那么此时多instance是必需的。 ）
> &emsp;&emsp;求解Sudoku用不了多大内存。 如果单线程编程更方便的话， 可以用单线程多进程来做。 再在前面加一个单线程的load balancer， 仿lighttpd＋fastcgi的成例。
> 
&emsp;&emsp; 线程不能减少工作量，即不能减少CPU时间。如果解决一个问题需要执行一亿条指令（这个数字不大， 不要被吓到），那么用多线程只会让这个数字增加。但是通过合理调配这一亿条指令在多个核上的执行情况，我们能让工期提早结束。 这听上去像统筹方法， 其实也确实是统筹方法。






&emsp;
&emsp;
&emsp;
# 三、关于线程的`bash`命令
## 
https://blog.csdn.net/XiaodunLP/article/details/99061696






&emsp;
&emsp;
&emsp;
# 参考文献
1. [Linux可以运行多少进程,一个进程可以开多少线程](https://www.cnblogs.com/bandaoyu/p/14625291.html)