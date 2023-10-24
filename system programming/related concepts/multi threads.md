[toc]






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
## 6. 单线程的`errno` 和 多线程的`errno` 有何区别？
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
&emsp;&emsp; 如果主线程调用了 `pthread_exit()`，而非调用 `exit()`或是执行 `return` 语句，那么其他线程将继续运行。
### 2.2 什么情况下会导致进程中的所有线程立即终止？
会导致进程中的所有线程立即终止的情况：
> (1) **任意线程** 调用了`exit()`;
> (2) **主线程** 在`main`函数中 执行了`return`语句
> 
### 2.3 有哪些方法可以终止线程？
总的来说，可以以如下方式终止线程的运行。
> (1) 线程 `start` 函数执行 `return` 语句并返回指定值。
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

### 3.5 `pthread_self()`返回的线程ID 是否存在复用？
&emsp;&emsp; 先说结论：`pthread_self()`返回的线程ID在`pthread_join()`后会复用。
&emsp;&emsp; `glibc`的`Pthreads`实现实际上把`pthread_t`用作一个结构体指针（它的类型是`unsigned long`），指向一块动态分配的内存，而且这块内存是反复使用的。这就造成`pthread_t`的值很容易重复。 `Pthreads`只保证同一进程之内，同一时刻的各个线程的id不同；不能保证同一进程先后多个线程具有不同的id， 更不要说一台机器上多个进程之间的id唯一性了。
&emsp;&emsp; 例如下面这段代码中先后两个线程的标识符是相同的：
```cpp
void* thread_func(void *arg) {   }

int main()
{
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_func, NULL);
    cout << t1 << endl;
    pthread_join(t1, NULL);

    pthread_create(&t2, NULL, thread_func, NULL);
    cout << t2 << endl; 
    pthread_join(t2, NULL);
}
```
运行结果如下：
```
140553174914816
140553174914816
```

### 3.6 `gettid()` 和 `pthread_self()`
#### 3.6.1 `gettid()` 和 `pthread_self()` 有何不一样？
**区别：** 
&emsp;&emsp; `gettid()` 获取的是内核中线程ID
&emsp;&emsp; `pthread_self` 是posix描述的线程ID。
> &emsp;&emsp; POSIX 线程 ID 与 Linux 专有的系统调用 `gettid()`所返回的线程ID 并不相同。 POSIX 线程 ID 由线程库实现来负责分配和维护。`gettid()`返回的线程 ID 是一个由内核（Kernel）分配的数字，类似于进程 ID（process ID）。虽然在 Linux NPTL 线程实现中，每个 POSIX 线程都对应一个唯一的内核线程 ID，但应用程序一般无需了解内核线程ID（况且，如果程序依赖于这一信息，也将无法移植）。
> 
#### 3.6.2 如果要通过线程ID来标识一个线程，应该使用哪个比较好？
&emsp;&emsp;见 [用到的理念、技术.md]()里的笔记


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
&emsp;&emsp; 我们都知道，线程若不被`join`，则将变成僵尸线程；
&emsp;&emsp; 若不希望不想调用`join`，又不想该线程最后变成僵尸进程，则需要调用`pthread_detach()`函数将该线程标记为 分离状态，处于分离状态的线程将在其终止时被OS自动清理，避免成为僵尸线程。
&emsp;&emsp; **总结**：线程分离可以理解为偷懒，因为处于分离状态的线程不需要`join`，OS会自动回收它，避免成为僵尸线程。

### 5.2 在什么情况下可以分离一个线程？如何分离一个线程？
&emsp;&emsp; 有时，程序员并不关心线程的返回状态，只是希望系统在线程终止时能够自动清理并移除之。在这种情况下，我们可以分离该线程(这样我们就不需要`join`该线程了)
&emsp;&emsp; 可以调用 `pthread_detach()`并向 `thread` 参数传入指定线程的标识符，将该线程标记为处于分离（detached）状态：
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
## 7. 多线程程序的初始化安全
### 7.1 多线程程序为什么需要注意初始化安全？
**多线程程序经常有这样的需求：** 不管创建了多少线程，有些初始化动作只能发生一次。例如：
> &emsp; 程序可能需要执行 `pthread_mutex_init()`对带有特殊属性的互斥量进行初始化，而且必须只能初始化一次：
> &emsp;&emsp;&emsp; (1) 如果由主线程来创建新线程，那么这一点易如反掌：可以在创建依赖于该初始化的线程之前进行初始化。
> &emsp;&emsp;&emsp; (2) 不过，对于库函数而言，这样处理就不可行，因为调用者在初次调用库函数之前可能已经创建了这些线程。故而需要这样的库函数：无论首次为任何线程所调用，都会执行初始化动作。
> 

### 7.2 多线程程序如何保证初始化安全？
可以通过函数 `pthread_once()`实现一次性初始化。
```cpp
#include <pthread.h>

pthread_once_t once_var = PTHREAD_ONCE_INIT;
// Returns 0 on success, or a positive error number on error
int pthread_once(pthread_once_t *once_control, void (*init)(void));
```
利用 **参数`once_control`** 的状态，函数`pthread_once()`可以确保无论有多少线程对`pthread_once()`调用了多少次，也只会执行一次由 `init` 指向的调用者定义函数；另外，参数 `once_control` 必须是一指针，指向初始化为 `PTHREAD_ONCE_INIT` 的静态变量：
```cpp
pthread_once_t once_var = PTHREAD_ONCE_INIT;
```
**init函数** 没有任何参数，形式如下：
```cpp
init(void){
	/* Function body */
}
```



&emsp;
## 8. 一个`Pthreads`的使用实例：连接任意已终止线程
### 8.1 原理
&emsp;&emsp; 前面已然提及，使用 `pthread_join()`只能连接一个指定线程。且该函数也未提供任何机制去连接任意的已终止线程。
&emsp;&emsp; 把所有线程放到一个数组里，当有一个线程终止的时候会通过条件变量发信号给主函数里的`pthread_cond_wait()`函数，主线程苏醒后遍历线程数组，检查有哪个线程的状态是 `TS_TERMINATED` ，然后将其join，然后回去继续 wait，直到所有线程均已join。因此，我们需要做如下几件事：
> (1) 需要定义一个 struct ，其中包含tid，线程状态都能；
> (2) 其它就和生产者和消费者中使用pthread_cond_wait()函数差不多；
> 

### 8.2 代码
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
		// 遍历，找出状态为TS_TERMINATED的线程，然后对该线程join
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
		// pthread_cond_wait() 会自动加锁
		s = pthread_mutex_unlock(&threadMutex);
		if (s != 0)
			errExitEN(s, "pthread_mutex_unlock");
	}
	exit(EXIT_SUCCESS);
}
```



&emsp; 
## 9. 线程取消(thread cancellation)
### 9.1  线程取消作用是？
&emsp;&emsp; 使用线程取消，一个线程 可以对 另一个线程 提出取消申请，即线程被动终止的一种情况。

### 9.2 什么时候 会使用 线程取消？
&emsp;&emsp; 有时候，需要将一个线程取消（cancel）。亦即，向线程发送一个请求，要求其立即退出。比如：
> &emsp;&emsp; ① 一组线程正在执行一个运算，一旦某个线程检测到错误发生，需要其他线程退出，取消线程的功能这时就派上用场。
> &emsp;&emsp; ②还有一种情况，一个由图形用户界面（GUI）驱动的应用程序可能会提供一个“取消”按钮，以便用户可以终止后台某一线程正在执行的任务。这种情况下，主线程（控制图形用户界面）需要请求后台线程退出。
> 

### 9.3 调用了线程取消，是不是就意味着目标线程立马退出？
&emsp;&emsp; 并不是，`pthread_cancel()`调用并不等待线程终止，它只提出请求。线程在取消请求(pthread_cancel)发出后会继续运行，直到到达某个取消点(取消点是线程检查是否被取消并按照请求进行动作的一个位置)。

### 9.4 取消线程 的权限


### 9.5 怎么使用 线程取消？
#### 9.5.1  Pthread 线程取消的 API
##### (1) 发送取消请求
函数 `pthread_cancel()`向 由`thread`指定的线程 发送一个取消请求：
```cpp
#include <pthread.h>
// Returns 0 on success, or a positive error number on error
int pthread_cancel(pthread_t thread);
```
发出取消请求后，函数 `pthread_cancel()`当即返回，不会等待目标线程的退出。
准确地说，目标线程会发生什么？何时发生？这些都取决于 线程取消状态（state）和 线程取消类型（type）。
##### (2) 设置 取消状态（Cancellation State） 
```cpp
#include <pthread.h>
// return 0 on success, or a positive error number on error
int pthread_setcancelstate(int state, int *oldstate);
```
函数 `pthread_setcancelstate()`会将调用线程的取消性状态置为**参数 state**所给定的值。该参数的值如下。
**① `PTHREAD_CANCEL_DISABLE`**
> 线程不可取消。如果此类线程收到取消请求，则会将请求挂起，直至将线程的取消状态置为启用。
> 
**② `PTHREAD_CANCEL_ENABLE`**
> 线程可以取消。这是新建线程取消性状态的默认值。
> 
&emsp;&emsp; 线程的前一取消性状态将返回至**参数 oldstate** 所指向的位置。
&emsp;&emsp; 如果对前一状态没有兴趣， Linux 允许将 `oldstate` 置为 `NULL`。在很多其他的系统实现中，情况也是如此。不过， SUSv3 并没有规范这一特性，所以要保证应用的可移植性，就不能依赖这一特性。应该总是为 oldstate 设置一个非 `NULL` 的值。
&emsp;&emsp; 如果线程执行的代码片段需要不间断地一气呵成，那么临时屏闭线程的取消性状态（`PTHREAD_CANCEL_DISABLE`）就变得很有必要。
&emsp;&emsp; 如果线程的取消性状态为“启用”（`PTHREAD_CANCEL_ENABLE`），那么对取消请求的处理则取决于线程的取消性类型，该类型可以通过调用函数 `pthread_setcanceltype()`时的参数`type` 给定。

##### (3) 设置  取消类型（Cancellation Type）
```cpp
#include <pthread.h>
// return 0 on success, or a positive error number on error
int pthread_setcanceltype(int type, int *oldtype);
```
**参数 type** 有如下值：
**① `PTHREAD_CANCEL_ASYNCHRONOUS`**
&emsp;&emsp; 可能会在任何时点（也许是立即取消，但不一定）取消线程。异步取消的应用场景很少。
**② `PTHREAD_CANCEL_DEFERED`**
&emsp;&emsp; 取消请求保持挂起状态，直至到达取消点（cancellation point，见下节）。这也是新建线程的缺省类型。后续各节将介绍延迟取消（deferred cancelability）的更多细节。线程原有的取消类型将返回至参数 oldtype 所指向的位置。
**参数 oldstate** ： 如果不关心原有取消类型， 许多系统实现（包括 Linux）允许将 `oldtype` 置为 NULL。同样， SUSv3 也没有规范这一行为，所以需要保障可移植性的应用不应使用这一特性，应该总是为 `oldtype` 设置一个非 `NULL` 值。

##### (4) 添加取消点
&emsp;&emsp; 假设线程执行的是一个不含取消点函数，这时，该线程永远也不会响应取消请求。此时可以使用函数 `pthread_testcancel()`来 创建一个取消点。线程如果已有处于挂起状态的取消请求，那么只要调用该函数，线程就会随之终止：
```cpp
#include <pthread.h>
void pthread_testcancel(void);
```
当线程执行的代码未包含取消点时，可以周期性地调用 `pthread_testcancel()`，以确保对其他线程向其发送的取消请求做出及时响应。

##### (5) 添加取消函数
```cpp
#include <pthread.h>
void pthread_cleanup_push(void (*routine)(void*), void *arg);
void pthread_cleanup_pop(int execute);
```

### 9.7 取消点(Cancellation Points)
#### 9.7.1 什么是取消点
&emsp;&emsp; 取消点就是响应外部取消请求（其它线程调用`pthread_cancel()`）的一个位置。

#### 9.7.2 取消点的作用？
&emsp;&emsp; 取消点 就是 目标线程 响应取消请求的一个位置，如果一个线程不包含取消点，即使有人给它发了取消请求，它也不会取消。
&emsp;&emsp; **也就是说，** 假如 线程B 对 线程A 发起取消请求，只有在 线程A 执行到了取消点，该线程才有可能被取消。举个例子：
> &emsp;&emsp; 城市里的公交车只在 特定的公交站（取消点）停车，当乘客（发起线程取消的线程B）叫司机停车时，司机（线程B）只有在公交站才会停车。
> 

#### 9.7.3 如何定义取消点？
**(1) 有一些特定的函数本身就是取消点**，比如：
<div align="center"> <img src="./pic/multi threads/Functions required to be cancellation points by SUSv3.png"> </div>

**(2) 自己添加一个取消点**
&emsp;&emsp; 函数 `pthread_testcancel()`可以产生一个取消点。

### 9.8 下列的代码可以取消吗？如果不能，该怎么修改？
```cpp
#include <stdio.h>    
#include <stdlib.h>    
#include <pthread.h>    
#include <unistd.h>    
  
void* func(void   *)   
{   
	pthread_setcancelstate(PTHREAD_CANCEL_ENABLE, NULL);           //允许退出线程   
	pthread_setcanceltype(PTHREAD_CANCEL_ASYNCHRONOUS,   NULL);   //设置立即取消  
	while (1) { }   
	return   NULL;   
}   
  
int main(int argc, char *argv[])   
{   
	pthread_t thrd;   
	pthread_attr_t attr;   
	pthread_attr_init(&attr);   
	pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);   
	
	if ( pthread_create(&thrd, &attr, func, NULL) )   {   
		perror( "pthread_create   error ");   
		exit(EXIT_FAILURE);   
	}   
	
	if (!pthread_cancel(thrd) )   {   
		printf("pthread_cancel OK\n ");   
	}   
	
	sleep(10);   
	return 0;   
}  
```
不会，因为`func()`函数不含取消点，子线程一直在`while()`循环，没有挂起，所以不能将其取消，如果要将其取消，需求添加一个取消点：
```cpp
void* func(void   *)   
{   
	pthread_setcancelstate(PTHREAD_CANCEL_ENABLE, NULL);           //允许退出线程   
	pthread_setcanceltype(PTHREAD_CANCEL_ASYNCHRONOUS,   NULL);   //设置立即取消  
	while (1) { 
		pthread_testcancel();
	}   
	return   NULL;   
}   
```

### 9.9 清理函数(cleanup handler)
#### 9.9.1 清理函数的 作用是？
&emsp;&emsp; 一旦有处于挂起状态的取消请求，线程在执行到取消点时如果只是草草收场，这会将共享变量以及 Pthreads 对象（例如互斥量）置于一种不一致状态，可能导致进程中其他线程产生错误结果、死锁，甚至造成程序崩溃。
&emsp;&emsp; 为了避免上述的情况，线程可以设置一个或多个清理函数，当线程遭取消时会自动运行这些函数，在线程终止之前可执行诸如修改全局变量，解锁互斥量等动作。
&emsp;&emsp; **线程取消会导致线程退出，OS不会帮忙释放资源，需要通过清理函数来释放资源。**

#### 9.9.2 清理函数的执行步骤
&emsp;&emsp; 每个线程都可以拥有一个清理函数栈。当线程遭取消时，会沿该栈自顶向下依次执行清理函数，首先会执行最近设置的函数，接着是次新的函数，以此类推。当执行完所有清理函数后，线程终止。
> 关于为什么清理函数的执行顺序是反着来的，可以这么理解：函数是记录在栈中的，这就意味着它们的执行顺序和它们注册时相反（后进先出）。
> 

#### 9.9.3 如何使用？
&emsp;&emsp; 见 TLPI 32.5节

### 能不能直接杀死进程？



&emsp;
## 10 线程栈(Thread Stacks)
### 10.1 什么是线程栈？作用是？
&emsp;&emsp; 创建线程时，每个线程都有一个属于自己的线程栈，且大小固定。
&emsp;&emsp; 线程栈是用来放 自动变量的，可以理解为线程自己的栈区。
<div align="center"> <img src="./pic/multi threads/memory model of a process which contained 4 threads.png"> </div>

### 10.2 同一进程中，每个线程的线程栈都一样大吗？
&emsp;&emsp; 不是，为了应对栈的增长），主线程栈的空间要大出许多，从上面的图就能看出来。

### 10.3 改变线程栈的大小
#### 10.3.1 为什么要改？
改变线程栈一般有两个原因：
* (1) 更大的线程栈可以容纳大型的自动变量或者深度的嵌套函数调用（也许是递归调用），这是改变每个线程栈大小的原因之一。
* (2) 而另一方面，应用程序可能希望减小每个线程栈，以便进程可以创建更多的线程：
> 系统可创建的线程数量为： `可支配的内存大小 / 线程默认栈大小`，因此减小默认线程栈的大小可以增加可创建的线程数量。
> 
#### 10.3.2 如何修改线程栈的大小？
有两个方法：
&emsp;&emsp; (1) 在通过线程属性对象创建线程时，调用函数`pthread_attr_setstacksize()`所设置的线程属性（29.8 节）决定了线程栈的大小。
&emsp;&emsp; (2) 而使用与之相关的另一函数 `pthread_attr_setstack()`，可以同时控制线程栈的大小和位置，不过设置栈的地址将降低程序的可移植性。



&emsp;
## 11. 给线程发送信号
见 TLPI 33.2节

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
# 三、可重入 和 线程安全(thread safe)
## 1. 可重入函数(reentrant function)
### 1.1 什么是可重入函数
&emsp;&emsp; 要解释可重入函数为何物，首先需要区分单线程程序和多线程程序。典型 UNIX 程序都具有一条执行线程，贯穿程序始终， CPU 围绕单条执行逻辑来处理指令。而对于多线程程序而言，同一进程却存在多条独立、并发的执行逻辑流。
&emsp;&emsp; 如果 <span style="color:red;"> 同一个进程的 </span><span style="color:green;">多条线程</span> 可以<span style="color:red;">同时安全地</span>调用某一函数，那么该函数就是可重入的。此处，“安全”意味着，无论其他线程调用该函数的执行状态如何，函数均可产生预期结果。
&emsp;&emsp;**SUSv3 对可重入函数的定义是**：函数由两条或多条线程调用时，即便是交叉执行，其效果也与各线程以未定义1顺序依次调用时一致。

### 1.2 什么样的函数是可重入函数？为什么？
* (1) 不能使用`malloc`系列函数，因为`malloc`函数内部是通过全局链表实现的
&emsp;&emsp;  `malloc()`和 `free()`维护有一个针对已释放内存块的链表，用于从堆中重新分配内存。如果主程序在调用 malloc()期间为一个同样调用 malloc()的信号处理器函数所中断，那么该链表可能会遭到破坏。因此，`malloc()`函数族以及使用它们的其他库函数都是不可重入的。
* (2) 不可以调用标准`I/O`库函数，这些库函数很多都不是可重入的
&emsp;&emsp; 就拿 `stdio`函数库成员 来说吧（`printf()`、 `scanf()`等），它们会为缓冲区 `I/O` 更新内部数据结构。所以，如果在信号处理器函数中调用了 `printf()`， 而主程序又在调用` printf()`或其他 `stdio` 函数期间遭到了处理器函数。
的中断，那么有时就会看到奇怪的输出，甚至导致程序崩溃或者数据的损坏。
* (3) 函数不能有全局或者静态变量，否则连线程安全都不满足了
&emsp;&emsp;更新全局变量或静态数据结构的函数可能是不可重入的。（只用到本地变量的函数肯定是可
重入的。）如果对函数的两个调用（例如：分别由两条执行线程发起）同时试图更新同一全局变量或数据类型，那么二者很可能会相互干扰并产生不正确的结果。例如，假设某线程正在为一链表数据结构添加一个新的链表项，而另一线程也正试图更新同一链表。由于为链表添加新项涉及对多枚指针的更新，一旦另一线程中断这些步骤并修改了相同的指针，结果就会产生混乱。



&emsp;
&emsp; 
## 2. 线程安全(thread safe)
### 2.1 什么是线程安全？
&emsp;&emsp; 若函数可同时供多个线程安全调用，则称之为线程安全函数；反之，如果函数不是线程安全的，则不能并发调用。依据[JCP]， 一个线程安全的class应当满足以下三个条件：
* (1)多个线程同时访问时， 其表现出正确的行为。
* (2)无论操作系统如何调度这些线程， 无论这些线程的执行顺序如何交织（interleaving） 。
* (3)调用端代码无须额外的同步或其他协调动作。
依据这个定义， C++标准库里的大多数class都不是线程安全的， 包括`std:: string`、`std::vector`、`std::map`等， 因为这些`class`通常需要在外部加锁才能供多个线程同时访问。
&emsp;&emsp; 从本质上来说，“线程安全”不是指线程的安全，而是指内存的安全。为什么如此说呢？这和操作系统有关。
&emsp;&emsp; 目前主流操作系统都是多任务的，即多个进程同时运行。为了保证安全，每个进程只能访问分配给自己的内存空间，而不能访问别的进程的，这是由操作系统保障的。
&emsp;&emsp; 在每个进程的内存空间中都会有一块特殊的公共区域，通常称为堆（内存）。进程内的所有线程都可以访问到该区域，这就是造成问题的潜在原因。
&emsp;&emsp; 假设某个线程把数据处理到一半，觉得很累，就去休息了一会，回来准备接着处理，却发现数据已经被修改了，不是自己离开时的样子了。可能被其它线程修改了。
&emsp;&emsp; 比如把你住的小区看作一个进程，小区里的道路/绿化等就属于公共区域。你拿1万块钱往地上一扔，就回家睡觉去了。睡醒后你打算去把它捡回来，发现钱已经不见了。可能被别人拿走了。因为公共区域人来人往，你放的东西在没有看管措施时，一定是不安全的。内存中的情况亦然如此。
&emsp;&emsp; 所以线程安全指的是，在堆内存中的数据由于可以被任何线程访问到，在没有限制的情况下存在被意外修改的风险。
&emsp;&emsp; 也就是说，堆内存空间在没有保护机制的情况下，对多线程来说是不安全的地方，因为你放进去的数据，可能被别的线程“破坏”。

### 2.2 线程安全的实现
#### 2.2.1 有哪些方法可以实现线程安全？它们各自有何优缺点？
**一共有四种方法：**
<span style="color:red;font-weight:bold"> ① 用同步原语(互斥锁、共享变量)来协调对共享资源的访问。 </span>

用互斥锁实现线程安全的不足：
> (1) 由于互斥量的加、解锁开销，故而也带来了性能的下降；
> (2) 造成并发性能的下降，因为同一时点只能有一个线程运行该函数，仅在函数中操作共享变量（临界区）的代码前后加入互斥锁可以提升并发性能，除非出现多个线程需要同时执行同一临界区的情况。
> 
<span style="color:red;font-weight:bold"> ① 线程特有数据(Thread-Specific Data) </span>

优点：
> 无需修改原有的函数接口；
> 
缺点：
> 写起来比较难
> 
<span style="color:red;font-weight:bold"> ③ 线程局部存储(Thread-Local Storage) </span>

优点：
> 比线程特有数据的使用要简单。要创建线程局部变量，只需简单地在全局或静态变量的声明中包含`__thread` 说明符即可。
> 也无需修改原有的函数接口；
> 
<span style="color:red;font-weight:bold"> ④ 将函数实现为可重入的 </span>

优点：
> 最有效的方式
> 
缺点：
> 对于一些已存在的库函数，需要修改其接口
> 

#### 2.2.2 如何 在不改变函数接口定义的情况下 实现线程安全？
&emsp;&emsp; 在**不改变函数接口定义的情况下**，保障不安全函数的线程安全有两种技术：
> ① 线程特有数据(Thread-Specific Data)   
> ② 线程局部存储(Thread-Local Storage)
> 
这两种技术均允许函数分配持久的、基于线程的存储。
&emsp;&emsp; **在单线程程序中**，我们经常要使用全局变量来实现多个函数间共享数据。**在多线程环境下**，由于数据空间是共享的，因此全局变量也为所有线程所共有。但有时在应用程序设计中有必要提供线程私有的全局变量，仅在某个线程中有效，但可以跨多个函数访问，这样每个线程访问它自己独立的数据空间，而不用担心和其它线程的同步访问。
> 比如：在程序中每个线程都使用同一个指针索引一个链表，并在多个函数内通过指针对链表进行操作，但是每个线程通过指针索引的链表都是自己独有的数据。
> 

#### 2.2.3  线程特有数据
POSIX 线程库提供了如下 API 来管理线程特有数据：
##### (1) 创建 key
```cpp
int pthread_key_create(pthread_key_t *key, void (*destructor)(void *));
```
> &emsp;&emsp; **第一参数 key** 指向 pthread_key_t 的对象的指针。请注意这里 pthread_key_t 的对象占用的空间是用户事先分配好的，pthread_key_create 不会动态生成 pthread_key_t 对象。
> &emsp;&emsp; **第二参数 desctructor**，如果这个参数不为空，那么当每个线程结束时，系统将调用这个函数来释放绑定在这个键上的内存块。
> 

##### (2) 动态数据初始化
有时我们在线程里初始化时，需要避免重复初始化。我们希望一个线程里只调用 pthread_key_create 一次，这时就要使用 pthread_once与它配合。
```cpp
int pthread_once(pthread_once_t *once_control, void (*init_routine)(void));
```
> &emsp;&emsp; 第**一个参数 once_control** 指向一个 pthread_once_t 对象，这个对象必须是常量 PTHREAD_ONCE_INIT，否则 pthread_once 函数会出现不可预料的结果。
> &emsp;&emsp; **第二个参数 init_routine**，是调用的初始化函数，不能有参数，不能有返回值。
如果成功则返回0，失败返回非0值。

##### (3) 键与线程数据关联
创建完键后，必须将其与线程数据关联起来。关联后也可以获得某一键对应的线程数据。关联键和数据使用的函数为：
```cpp
int pthread_setspecific(pthread_key_t *key, const void *value);
```
第一参数 key 指向键。
第二参数 value 是欲关联的数据。
函数成功则返回0，失败返回非0值。

注意：用 pthread_setspecific 为一个键指定新的线程数据时，并不会主动调用析构函数释放之前的内存，所以调用线程必须自己释放原有的线程数据以回收内存。

##### (4) 获取键管理的线程数据
获取与某一个键关联的数据使用函数的函数为：
```cpp
void *pthread_getspecific(pthread_key_t *key);
```
参数 key 指向键。
如果有与此键对应的数据，则函数返回该数据，否则返回NULL。

##### (5) 删除一个键
删除一个键使用的函数为：
```cpp
int pthread_key_delete(pthread_key_t key);
```
##### (6) 一个实例(摘自TLPI)
```cpp
#define _GNU_SOURCE /* Get '_sys_nerr' and '_sys_errlist' declarations from <stdio.h> */

#include <stdio.h>
#include <string.h> /* Get declaration of strerror() */
#include <pthread.h>
#include "tlpi_hdr.h"

static pthread_once_t once = PTHREAD_ONCE_INIT;
static pthread_key_t strerrorKey;

/* Maximum length of string in per-thread buffer returned by strerror() */
#define MAX_ERROR_LEN 256 

static void /* Free thread-specific data buffer */
destructor(void *buf){
	free(buf);
}

static void /* One-time key creation function */
 createKey(void)
{
	int s;

	/* Allocate a unique thread-specific data key and save the address
	of the destructor for thread-specific data buffers */

	s = pthread_key_create(&strerrorKey, destructor);
	if (s != 0)
		errExitEN(s, "pthread_key_create");
}


char *
strerror(int err)
{
	int s;
	char *buf;
	/* Make first caller allocate key for thread-specific data */
	s = pthread_once(&once, createKey);
	if (s != 0)
		errExitEN(s, "pthread_once");
	buf = pthread_getspecific(strerrorKey);
	if (buf == NULL) { /* If first call from this thread, allocate
						buffer for thread, and save its location */
		buf = malloc(MAX_ERROR_LEN);
		if (buf == NULL)
			errExit("malloc");
		s = pthread_setspecific(strerrorKey, buf);
		if (s != 0)
			errExitEN(s, "pthread_setspecific");
	}
	if (err < 0 || err >= _sys_nerr || _sys_errlist[err] == NULL) {
		snprintf(buf, MAX_ERROR_LEN, "Unknown error %d", err);
	} else {
		strncpy(buf, _sys_errlist[err], MAX_ERROR_LEN - 1);
		buf[MAX_ERROR_LEN - 1] = '\0'; /* Ensure null termination */
	}

	return buf;
}
```

#### 2.2.4 线程局部存储(Thread-Local Storage)
##### (1) 线程局部存储 的作用是？
&emsp;&emsp; 类似于线程特有数据，线程局部存储提供了持久的每线程存储。作为非标准特性，诸多其他的 UNIX 实现（例如 Solaris 和 FreeBSD）为其提供了相同，或类似的接口形式。
##### (2) 线程局部存储的优点是什么？
&emsp;&emsp; **线程局部存储的主要优点在于**，比线程特有数据的使用要简单。
##### (3) 如何使用 线程局部存储？
要创建线程局部变量，只需简单地在 **全局或静态变量的声明中** 包含`__thread` 说明符即可：
```cpp
static __thread buf[MAX_ERROR_LEN];
```
&emsp;&emsp; 但凡带有这种说明符的变量，每个线程都拥有一份对变量的拷贝。线程局部存储中的变量将一直存在，直至线程终止，届时会自动释放这一存储。
&emsp;&emsp; 关于线程局部变量的声明和使用，需要注意如下几点。
* ① 如果变量声明中使用了关键字 static 或 extern，那么关键字__thread 必须紧随其后。
* ② 与一般的全局或静态变量声明一样，线程局部变量在声明时可设置一个初始值。
* ③ 可以使用 C 语言取址操作符（&）来获取线程局部变量的地址。
* ④  __thread 只能修饰`POD`变量，简单的来说可以是如下几种变量
	> 1). 基本类型 (int , float 等等)
	> 2). 指针类型
	> 3). 不带自定义构造函数和析构函数的类，如果希望修饰带自定义构造和析构函数的类，需要用到指针。
	
##### (4) 一个实例
```cpp
#include <iostream>
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>
#include <assert.h>
#include <string>

class Student {
public:
    //Student(int a , int b):num(a), age(b) { }
    //Student();
    int num;
    int age;
};

class Teacher {
public:
   int num;
   int age;
   // 注意，
   Teacher (int num, int age) {
      this->num = num;
      this->age = age;
   }
};


static __thread int count;
static __thread Student stu;
static __thread Teacher* teacher;

void print(std::string description, int num, Student stu, Teacher* teacher) {
    std::cout << description << ", before======>" << "pid:" 
        << getpid() << ", pthread id:" << pthread_self() 
        << ", count:" << count << std::endl;
    std::cout << description << ", before======>" << "pid:" 
        << getpid() << ", pthread id:" << pthread_self() 
        << ", stu.num:" << stu.num << ", stu.age:"<< stu.age << std::endl;
    std::cout << description << ", before======>" << "pid:" 
        << getpid() << ", pthread id:" << pthread_self() 
        << ", teacher.num:" << teacher->num << ", teacher.age:"
		<< teacher->age << std::endl << std::endl;
}


void *function1(void *argc)
{
    count = 2;
    stu.num = 20210010;
    stu.age = 17;
    teacher = new Teacher(10180,30);
    sleep(1);
    print("thread1", count, stu, teacher);
    return 0;
}

void *function2(void *argc)
{
    stu.num = 20210199;
    stu.age = 12;
    count = 3;
    teacher = new Teacher(10008, 49);
    sleep(2);
    print("thread2", count, stu, teacher);
    return 0;
}

int main()
{
    pthread_t  thread_id[2];
    int ret;

    stu.num = 1;
    stu.age = 1;
    count = 1;
    teacher = new Teacher(1,1);

    print("main", count, stu, teacher);
    pthread_create(thread_id, NULL, function1, NULL);
    pthread_create(thread_id + 1, NULL, function2, NULL);
    pthread_join(thread_id[0], NULL);
    pthread_join(thread_id[1], NULL);
    //print("main", count, stu, teacher);

    return 0;
}
```
编译后运行：
```
main, before======>pid:240852, pthread id:140372816856960, count:1
main, before======>pid:240852, pthread id:140372816856960, stu.num:1, stu.age:1
main, before======>pid:240852, pthread id:140372816856960, teacher.num:1, teacher.age:1

thread1, before======>pid:240852, pthread id:140372798891776, count:2
thread1, before======>pid:240852, pthread id:140372798891776, stu.num:20210010, stu.age:17
thread1, before======>pid:240852, pthread id:140372798891776, teacher.num:10180, teacher.age:30

thread2, before======>pid:240852, pthread id:140372790499072, count:3
thread2, before======>pid:240852, pthread id:140372790499072, stu.num:20210199, stu.age:12
thread2, before======>pid:240852, pthread id:140372790499072, teacher.num:10008, teacher.age:49
```
可以看到，主线程、线程1、线程2 所看到的几个`__thread`变量的内容都不一样。
**分析：**
> 1). `__thread` 可以修饰`Student`, 因为`Student`没有自定义的构造和析构函数
> 2). `__thread` 可以修饰`Teacher*`，因为`__thread`可以修饰指针
> 3). 实验结果表示在不同的线程中，变量不会相互影响。
> 

##### (5) 如何用 `__thread` 来声明自己的类类型？
如果要用`__thread`来自定义类类型，有如下两个方法：
**① 类里面不要有任何自定义的构造函数**
就拿上面的实例中的`Student`类来说，如果将其改为如下形式：
```cpp
class Student {
public:
    //Student(int a , int b):num(a), age(b) { }
    Student();
    int num;
    int age;
};
```
编译后报错如下：
```
test.cpp:29:25: error: non-local variable ‘stu’ declared ‘__thread’ needs dynamic initialization
 static __thread Student stu;
                         ^~~
test.cpp:29:25: note: C++11 ‘thread_local’ allows dynamic initialization and destruction
```
所以说，如果用`__thread` 来声明自己的类类型，就不能自己提供构造函数，即使是自己提供默认构造函数也不行！

**② 定义一个指向该类的指针**
就拿上面的实例中的`Teacher`类来说，
```cpp
class Teacher {
public:
   int num;
   int age;
   // 注意，
   Teacher (int num, int age) {
      this->num = num;
      this->age = age;
   }
};
```
`Teacher`类含有了自己的构造函数，于是代码通过定义一个`Teacher*`类型的指针来完成：
```cpp
static __thread Teacher* teacher;

int main()
{
    // 略...

    teacher = new Teacher(1,1);

	// 略...
}
```
我们从之前的执行结果也可以看出这个方法管用。

### 2.3 有哪些同步原语可以用在线程上？它们有何特点？
&emsp;&emsp; 线程提供的强大共享是有代价的。多线程应用程序必须使用互斥量和条件变量等同步原语来协调对共享变量的访问：
* **互斥量(mutex,即mutual exclusion)** 可以帮助线程同步对共享资源的使用，以防如下情况发生：线程某甲试图访问一共享变量时，线程某乙正在对其进行修改。
* **条件变量(condition variable)** 则是在此之外的拾遗补缺，允许线程相互通知共享变量（或其他共享资源）的状态发生了变化。

### 2.4 如何使用 同步原语 来保证线程安全？
见 《Linux/UNIX系统编程手册》 第30章 线程同步

### 2.5 用互斥锁等同步原语实现线程安全有何不足？
* (1) 由于互斥量的加、解锁开销，故而也带来了性能的下降；
* (2) 造成并发性能的下降，因为同一时点只能有一个线程运行该函数，仅在函数中操作共享变量（临界区）的代码前后加入互斥锁可以提升并发性能，除非出现多个线程需要同时执行同一临界区的情况。

### 2.6 `malloc()`函数是否线程安全？
&emsp;&emsp; `malloc()` 函数库中的函数数为堆中的空闲块维护有一个全局链表，显然它是不可重入的，但是过使用互斥，`malloc()`实现了线程安全。



&emsp; 
## 3. 可重入函数 和 线程安全函数 有何异同？
&emsp;&emsp; 两者不是等价的概念，可重入更严格。
&emsp;&emsp; 如果一个函数的实现 使用了全局或者静态变量，那么这个函数既不是可重入的，也不是线程安全的。
&emsp;&emsp; 如果放宽条件，这个函数仍然用到了全局或者静态变量，但是在访问这些变量时，通过加锁来保证互斥访问，那么这个函数就可以变成线程安全的函数。但它此时仍然是不可重入的，因为通常加锁是针对不同线程的访问，对同一线程可能出现问题（发生信号软中断，signal handler中恰巧也执行了该函数）。
&emsp;&emsp; 那么如果把函数中的全局和静态变量都干掉，并保证在该函数中也不调用不可重入的函数，那么这个函数可以做到既是线程安全的，也是可重入的。
&emsp;&emsp; 综上，可重入函数一般都是线程安全的，线程安全的不一定是可重入的。
<div align="center"> <img src="./pic/reentrant_threadSafe.png"> </div>
<center> <font color=black> <b> 图2 线程安全和可重入的关系 </b> </font> </center>



&emsp; 
## 4. 异步信号安全函数
&emsp;&emsp; 异步信号安全的函数是指当从信号处理器函数调用时，可以保证其实现是安全的。如果某一函数是可重入的，又或者信号处理器函数无法将其中断时，就称该函数是异步信号安全的。
详见 《Linux/UNIX系统编程手册》 第21章 信号：信号处理器函数（pdf第380页）





&emsp;
&emsp;
&emsp;
# 四、关于多线程的一些问题的解答（摘自muduo的书中）
## 1．Linux下能启动的线程数量
### 1.1 Linux能同时启动多少个线程？
系统能创建的线程数量受下面三个影响：
* ① `/usr/include/bits/local_lim.h`中的PTHREAD_THREADS_MAX限制了进程的最大线程数
* ② `/proc/sys/kernel/threads-max`中限制了系统的最大线程数
* ③ 内存的大小，因为一个线程的默认栈为`8Mb`或`10Mb`，从内存大小的的角度来看，系统支持的最大线程数量为：`可支配的内存大小 / 线程默认栈大小`

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
2. [可重入和线程安全](https://www.cnblogs.com/lenomirei/p/5666154.html)
3. [什么是线程安全](https://zhuanlan.zhihu.com/p/67905621)
4. [异步可重入函数与线程安全函数等价吗？](https://www.zhihu.com/question/21526405/answer/37330407)
5. [《Linux/UNIX系统编程手册》](https://book.douban.com/subject/25809330/)
6. [线程特有数据(Thread Specific Data)](https://www.jianshu.com/p/61c2d33877f4)
7. [c/c++ __thread](https://blog.csdn.net/simsunny22/article/details/82597859)