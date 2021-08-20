# 什么是`NTPL`？
NPTL(Native POSIX Threads Library)，这是 Linux 线程实现的现代版，Linux 2.6之后的都是NTPL线程。它的前任是`LinuxThreads`，是最初的 Linux 线程实现，先已被`NTPL`取代。

设计 NPTL 是为了弥补 `LinuxThreads` 的大部分的缺陷。特别是如下部分：
> NPTL 更接近 SUSv3 Pthreads 标准。
> 使用 NPTL 的有大量线程的应用程序的性能要远优于 LinuxThreads。
> 






&emsp;
&emsp;
&emsp;
# 二、关于多线程的一些问题的解答
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