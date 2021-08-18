- [一、 Reactor](#一-reactor)
  - [1. 介绍](#1-介绍)
    - [1.1 什么是Reactor？](#11-什么是reactor)
  - [2 关于Reactor的 几个问题](#2-关于reactor的-几个问题)
    - [2.1  Reactor 和 IO多路复用 的关系是？](#21--reactor-和-io多路复用-的关系是)
    - [2.2 Reactor 解决的是什么问题？为什么需要 Reactor ？](#22-reactor-解决的是什么问题为什么需要-reactor-)
    - [2.3 Reactor模式 主要由哪几部分组成？它们的作用分别是？](#23-reactor模式-主要由哪几部分组成它们的作用分别是)
  - [3 Reactor模式有哪些实现方式？](#3-reactor模式有哪些实现方式)
  - [4 Reactor模式 的工作原理是怎样的？](#4-reactor模式-的工作原理是怎样的)
    - [4.1 单 Reactor + 单进程/线程 处理资源池](#41-单-reactor--单进程线程-处理资源池)
      - [4.1.1 如何实现？](#411-如何实现)
      - [4.1.2 单Reactor + 单进程/线程 处理资源池 的优缺点是什么？](#412-单reactor--单进程线程-处理资源池-的优缺点是什么)
      - [4.1.3 单Reactor + 单进程/线程 处理资源池 适合什么样的场景？](#413-单reactor--单进程线程-处理资源池-适合什么样的场景)
    - [4.2 单 Reactor + 多进程/线程 处理资源池](#42-单-reactor--多进程线程-处理资源池)
      - [4.2.1 如何实现？](#421-如何实现)
      - [4.2.2 单Reactor + 多进程/线程处理资源池 优缺点是什么？](#422-单reactor--多进程线程处理资源池-优缺点是什么)
      - [4.2.3 单Reactor + `多进程`处理资源池 和 单Reactor + 多进`线程`处理资源池，哪个用的多？为什么？](#423-单reactor--多进程处理资源池-和-单reactor--多进线程处理资源池哪个用的多为什么)
    - [4.3 多Reactor + 多进程/线程 处理资源池](#43-多reactor--多进程线程-处理资源池)
      - [4.3.1 如何实现？](#431-如何实现)
  - [5. Reactor模式中，哪个线程/进程 负责将响应报文 发给client？](#5-reactor模式中哪个线程进程-负责将响应报文-发给client)
  - [6 有哪些软件使用的是Reactor？](#6-有哪些软件使用的是reactor)
- [二、 Proactor](#二-proactor)
  - [1. 什么是 Proactor](#1-什么是-proactor)
  - [2. Proactor 工作原理](#2-proactor-工作原理)
  - [3. 在工作中是否应该尽量使用 Proactor？](#3-在工作中是否应该尽量使用-proactor)
- [三、Reactor 和 Proactor 对比](#三reactor-和-proactor-对比)
  - [1. Reactor 和 Proactor 有何区别？](#1-reactor-和-proactor-有何区别)
  - [2. Reactor 和 Proactor 哪种是基于事件分发的？](#2-reactor-和-proactor-哪种是基于事件分发的)
  - [2. 工作中应该 使用 Reactor 还是 Proactor ？](#2-工作中应该-使用-reactor-还是-proactor-)
- [四 总结](#四-总结)
- [参考文献](#参考文献)



# 一、 Reactor
## 1. 介绍
### 1.1 什么是Reactor？
&emsp;&emsp; Reactor中文名为 反应堆模式，也叫 **Dispatcher模式**，即 I/O 多路复用监听事件，收到事件后，根据事件类型分配（Dispatch）给某个线程。
&emsp;&emsp; Reactor模式是处理并发I/O比较常见的一种模式，用于同步I/O，中心思想是将所有要处理的I/O事件注册到一个中心I/O多路复用器上，同时主线程/进程阻塞在多路复用器上；一旦有I/O事件到来或是准备就绪(文件描述符或socket可读、写)，多路复用器返回并将事先注册的相应I/O事件分发到对应的处理器中。
&emsp;&emsp; Reactor是一种事件驱动机制，和普通函数调用的不同之处在于：应用程序不是主动的调用某个API完成处理，而是恰恰相反，Reactor逆置了事件处理流程，应用程序需要提供相应的接口并注册到Reactor上，如果相应的事件发生，Reactor将主动调用应用程序注册的接口，这些接口又称为“回调函数”。用“好莱坞原则”来形容Reactor再合适不过了：不要打电话给我们，我们会打电话通知你。
&emsp;&emsp; **其实可以简单粗暴的把Reactor理解为 对IO多路复用的封装。**

## 2 关于Reactor的 几个问题
### 2.1  Reactor 和 IO多路复用 的关系是？
&emsp;&emsp; Reactor其实就是对 IO多路复用的一层封装，IO复用函数`epoll poll select` 是对io进行管理，而reactor将对io的管理转化为对事件的管理。
&emsp;&emsp; 之所以要这样封装一下，是因为直接使用 I/O多路复用接口编写网络程序很繁琐，开发效率也不高。于是，大佬们基于面向对象的思想，对 I/O 多路复用作了一层封装，让使用者不用考虑底层网络 API 的细节，只需要关注应用代码的编写。

### 2.2 Reactor 解决的是什么问题？为什么需要 Reactor ？
&emsp;&emsp; **在传统的 网络编程中**，线程在真正处理请求之前首先需要从 `socket` 中读取网络请求，而在读取完成之前，线程本身被阻塞，不能做任何事，这就导致线程资源被占用，而线程资源本身是很珍贵的，尤其是在处理高并发请求时。
&emsp;&emsp; **而 Reactor 模式指出**，在等待 IO 时，线程可以先退出，这样就不会因为有线程在等待 IO 而占用资源。但是这样原先的执行流程就没法还原了，因此，我们可以利用事件驱动的方式，要求线程在退出之前向 event loop 注册回调函数，这样 IO 完成时 event loop 就可以调用回调函数完成剩余的操作。
**所以说，Reactor模式 通过减少服务器的资源消耗，提高了并发的能力。**

### 2.3 Reactor模式 主要由哪几部分组成？它们的作用分别是？
Reactor模式由 Reactor、Acceptor和Handler构成，它们负责的事情如下：
> **(1) Reactor** 负责监听和分发事件，事件类型包含连接事件、读写事件，可以将其看作是 IO事件的派发者；
> **(2) Acceptor** 接受client连接
> **(3) Handler** 处理资源池负责处理事件，如： `read —> 业务逻辑 —> send`；
> 


## 3 Reactor模式有哪些实现方式？
Reactor模式是灵活多变的，可以应对不同的业务场景，灵活在于：
> Reactor 的数量可以只有一个，也可以有多个；
> 处理资源池 可以是 单个进程/线程，也可以是 多个进程/线程；
> 
将上面的两个因素排列组设一下，理论上就可以有 4 种方案选择：
> 单 Reactor + 单进程/线程 处理资源池；
> 单 Reactor + 多进程/线程 处理资源池；
> 多 Reactor + 单进程/线程 处理资源池；
> 多 Reactor + 多进程/线程 处理资源池
> 
其中，「_多 Reactor + 单进程/线程 处理资源池_」实现方案相比「_单 Reactor + 单进程/线程 处理资源池_」方案，不仅复杂而且也没有性能优势，因此实际中并没有应用。而剩下的 3 个方案都是比较经典的，且都有应用在实际的项目中。
方案具体使用进程还是线程，要看使用的编程语言以及平台有关：
> Java 语言一般使用线程，比如 Netty;
> C 语言使用进程和线程都可以，例如 Nginx 使用的是进程，Memcache 使用的是线程。
> 

## 4 Reactor模式 的工作原理是怎样的？
前面已经提到，Reactor模式 常用的几种方式如下：
> 单 Reactor + 单进程/线程 处理资源池；
> 单 Reactor + 多进程/线程 处理资源池；
> 多 Reactor + 多进程/线程 处理资源池
> 
### 4.1 单 Reactor + 单进程/线程 处理资源池
#### 4.1.1 如何实现？
我们来看看「单 Reactor + 单进程/线程」的方案示意图：
<div align="center"><img src="./pic/Reactor Proactor/pic1.jpg"></div>

可以看到进程里有 `Reactor、Acceptor、Handler` 这三个对象：
> `Reactor` 对象的作用是 监听和分发事件；
> `Acceptor` 对象的作用是 获取连接；
> `Handler` 对象的作用是 处理业务；
> 
对象里的 `select、accept、read、send` 是系统调用函数，`dispatch` 和 「业务处理」是需要完成的操作，其中 `dispatch` 是分发事件操作。
接下来，介绍下「单 Reactor 单进程」这个方案：
> Reactor 对象通过 select （IO 多路复用接口） 监听事件，收到事件后通过 dispatch 进行分发，具体分发给 Acceptor 对象还是 Handler 对象，还要看收到的事件类型：
> &emsp;&emsp; ① 如果是连接建立的事件，则交由 Acceptor 对象进行处理，Acceptor 对象会通过 accept 方法 获取连接，并创建一个 Handler 对象来处理后续的响应事件；
> &emsp;&emsp; ② 如果不是连接建立事件，则交由当前连接对应的 Handler 对象来进行响应；Handler 对象通过 read -> 业务处理 -> send 的流程来完成完整的业务流程。
> 

#### 4.1.2 单Reactor + 单进程/线程 处理资源池 的优缺点是什么？
**优点：**
> 单 Reactor 单进程的方案因为全部工作都在同一个进程内完成，所以实现起来比较简单，不需要考虑进程间通信，也不用担心多进程竞争。
> 
**缺点：**
> ① 因为只有一个进程，无法充分利用 多核 CPU 的性能；
> ② Handler 对象在业务处理时，整个进程是无法处理其他连接的事件的，如果业务处理耗时比较长，那么就造成响应的延迟；
> 
#### 4.1.3 单Reactor + 单进程/线程 处理资源池 适合什么样的场景？
&emsp;&emsp; 单Reactor 单进程的方案 不适用计算机密集型的场景，只适用于业务处理非常快速的场景。
&emsp;&emsp; Redis 是由 C 语言实现的，它采用的正是「单 Reactor 单进程」的方案（注意是单进程哦，不是单线程），因为 Redis 业务处理主要是在内存中完成，操作的速度是很快的，性能瓶颈不在 CPU 上，所以 Redis 对于命令的处理是单进程的方案。

&emsp;
### 4.2 单 Reactor + 多进程/线程 处理资源池
#### 4.2.1 如何实现？
&emsp;&emsp; 如果要克服「单Reactor 单线程/进程」方案的缺点，那么就需要引入 多线程/多进程，这样就产生了单Reactor 多线程/多进程 的方案。
&emsp;&emsp; 先来看看「单 Reactor 多线程」方案的示意图如下：
<div align="center"><img src="./pic/Reactor Proactor/pic2.jpg"></div>

下面详细说一下这个方案：
> Reactor 对象通过 select （IO 多路复用接口） 监听事件，收到事件后通过 dispatch 进行分发，具体分发给 Acceptor 对象还是 Handler 对象，还要看收到的事件类型；
> &emsp;&emsp; ① 如果是连接建立的事件，则交由 Acceptor 对象进行处理，Acceptor 对象会通过 accept 方法 获取连接，并创建一个 Handler 对象来处理后续的响应事件；
> &emsp;&emsp; ② 如果不是连接建立事件， 则交由当前连接对应的 Handler 对象来进行响应；
> 
上面的三个步骤和 _单 Reactor 单线程方案_ 是一样的，接下来的步骤就开始不一样了：
> Handler 对象不再负责业务处理，只负责数据的接收和发送，Handler 对象通过 read 读取到数据后，会将数据发给子线程里的 Processor 对象进行业务处理；
> 子线程里的 Processor 对象就进行业务处理，处理完后，将结果发给主线程中的 Handler 对象，接着由 Handler 通过 send 方法将响应结果发送给 client；
> 

#### 4.2.2 单Reactor + 多进程/线程处理资源池 优缺点是什么？
**优点：**
> 单 Reator 多线程/进程的方案优势在于能够充分利用多核 CPU 的性能
> 
**缺点：**
> 引入多线程/进程，那么自然就带来了多线程竞争资源的问题
> 一个 Reactor 对象承担所有事件的监听和响应，而且只在主线程中运行，在面对瞬间高并发的场景时，容易成为性能的瓶颈的地方。
> 

#### 4.2.3 单Reactor + `多进程`处理资源池 和 单Reactor + 多进`线程`处理资源池，哪个用的多？为什么？
&emsp;&emsp; 单Reactor + 多进`线程`处理资源池 用的多，这是由于 单Reactor 多进程 相比 单 Reactor 多线程 实现起来很麻烦，主要因为：
> &emsp;&emsp; **多进程**要考虑 子进程 和 父进程的双向通信，并且父进程还得知道子进程要将数据发送给哪个客户端。
> &emsp;&emsp; 而**多线程**间可以共享数据，虽然要额外考虑并发问题，但是这远比进程间通信的复杂度低得多，因此实际应用中也看不到单 Reactor 多进程的模式。
> 


&emsp;
### 4.3 多Reactor + 多进程/线程 处理资源池
&emsp;&emsp; 要解决「单 Reactor」面对瞬间高并发的场景时，容易成为性能的瓶颈的问题，就需要将「单 Reactor」实现成「多 Reactor」，这样就产生了 多Reactor + 多进程/线程处理资源池 的方案。
#### 4.3.1 如何实现？
多Reactor + 多进程/线程处理资源池 方案示意图如下：
<div align="center"><img src="./pic/Reactor Proactor/pic3.jpg"></div>

方案详细说明如下：
> 主线程中的 MainReactor 对象通过 select 监控连接建立事件，收到事件后通过 Acceptor 对象中的 accept 获取连接，将新的连接分配给某个子线程；
> 子线程中的 SubReactor 对象将 MainReactor 对象分配的连接加入 select 继续进行监听，并创建一个 Handler 用于处理连接的响应事件。
> 如果有新的事件发生时，SubReactor 对象会调用当前连接对应的 Handler 对象来进行响应。
> Handler 对象通过 read -> 业务处理 -> send 的流程来完成完整的业务流程。
> 
多 Reactor 多线程的方案虽然看起来复杂的，但是实际实现时比单 Reactor 多线程的方案要简单的多，原因如下：
> 主线程和子线程分工明确，主线程只负责接收新连接，子线程负责完成后续的业务处理。
> 主线程和子线程的交互很简单，主线程只需要把新连接传给子线程，子线程无须返回数据，直接就可以在子线程将处理结果发送给客户端。
> 
网上还有这样实现方式，示意图如下：
<div align="center"><img src="./pic/Reactor Proactor/pic4.jpg"></div>

**方案说明:**
> (1) Reactor主线程 MainReactor 对象通过select 监听连接事件, 收到事件后，通过Acceptor 处理连接事件
> (2) 当 Acceptor 处理连接事件后，MainReactor 将连接分配给SubReactor
> (3) subreactor 将连接加入到连接队列进行监听,并创建handler进行各种事件处理
> (4) 当有新事件发生时， subreactor 就会调用对应的handler处理
> (5) handler 通过read 读取数据，分发给后面的worker 线程处理
> (6) worker 线程池分配独立的worker 线程进行业务处理，并返回结果
> (7) handler收到响应的结果后，再通过send 将结果返回给client
> (8) Reactor主线程可以对应多个Reactor 子线程, 即MainRecator 可以关联多个SubReactor
> 
它和前面那种实现方式的区别是：
> 前面的那种实现 把`read`、`send`和业务处理都放在`Handler`线程
> 后面那种实现 `Handler`线程只处理`read`和`send`，不处理业务；
> 

&emsp;
## 5. Reactor模式中，哪个线程/进程 负责将响应报文 发给client？
&emsp;&emsp; 对于上面三种Reactor实现，都是通过`Handler`线程 来`send`响应报文，从图就能看出来。

&emsp;
## 6 有哪些软件使用的是Reactor？
**(1) Redis**：
> Redis 是由 C 语言实现的，它采用的正是「单 Reactor 单进程」的方案（注意是单进程哦，不是单线程），因为 Redis 业务处理主要是在内存中完成，操作的速度是很快的，性能瓶颈不在 CPU 上，所以 Redis 对于命令的处理是单进程的方案。
>
**(2) Netty 和 Memcache** ：大名鼎鼎的两个开源软件 都采用了「多 Reactor 多线程」的方案。
**(3) Nginx**：采用了「多Reactor 多进程」，不过方案与标准的多 Reactor 多进程有些差异：
> 具体差异表现在 主进程中仅仅用来初始化 socket，并没有创建 mainReactor 来 accept 连接，而是由子进程的 Reactor 来 accept 连接，通过锁来控制一次只有一个子进程进行 accept（防止出现惊群现象），子进程 accept 新连接后就放到自己的 Reactor 进行处理，不会再分配给其他子进程。
> 






&emsp;
&emsp;
&emsp;
# 二、 Proactor
&emsp;&emsp; 前面提到的 `Reactor` 是非阻塞同步IO，而 `Proactor` 采用了异步 I/O 技术，所以被称为异步网络模型。
## 1. 什么是 Proactor
&emsp;&emsp; 可以简单粗暴的将 Proactor 理解为 对异步IO函数的封装，以此实现了异步IO。
## 2. Proactor 工作原理
<div align="center"><img src="./pic/Reactor Proactor/pic5.jpg"></div>

介绍一下 Proactor 模式的工作流程：
> (1) Proactor Initiator 负责创建 Proactor 和 Handler 对象，并将 Proactor 和 Handler 都通过 Asynchronous Operation Processor 注册到内核；
> (2) Asynchronous Operation Processor 负责处理注册请求，并处理 I/O 操作；
> (3) Asynchronous Operation Processor 完成 I/O 操作后通知 Proactor；
> (4) Proactor 根据不同的事件类型回调不同的 Handler 进行业务处理；
> (5) Handler 完成业务处理；
> 

## 3. 在工作中是否应该尽量使用 Proactor？
&emsp;&emsp; Proactor模式提供了一个很好的思路，但是可惜的是，在 Linux 下的异步 I/O 是不完善的， aio 系列函数是由 POSIX 定义的异步操作接口，不是真正的操作系统级别支持的，而是在用户空间模拟出来的异步，并且仅仅支持基于本地文件的 aio 异步操作，网络编程中的 socket 是不支持的，这也使得基于 Linux 的高性能网络程序都是使用 Reactor 方案。
&emsp;&emsp; 而 Windows 里实现了一套完整的支持 socket 的异步编程接口，这套接口就是 IOCP，是由操作系统级别实现的异步 I/O，真正意义上异步 I/O，因此在 Windows 里实现高性能网络程序可以使用效率更高的 Proactor 方案。







&emsp;
&emsp;
&emsp;
# 三、Reactor 和 Proactor 对比
## 1. Reactor 和 Proactor 有何区别？
 `Reactor` 是非阻塞同步IO，而 `Proactor` 采用了异步 I/O 技术，现在我们再来理解 Reactor 和 Proactor 的区别，就比较清晰了。
**Reactor 是非阻塞同步网络模式**，感知的是 <span style="color:red; font-size:18px; font-weight:bold"> 就绪 可读写事件 </span>：
> &emsp;&emsp; 在每次感知到有事件发生（比如 可读就绪事件）后，就需要应用进程主动调用 read 方法来完成数据的读取，也就是要应用进程主动将 socket 接收缓存中的数据读到应用进程内存中，这个过程是同步的，读取完数据后应用进程才能处理数据。
> 
**Proactor 是异步网络模式**， 感知的是 <span style="color:red; font-size:18px; font-weight:bold"> 已完成 的读写事件 </span>：
> &emsp;&emsp; 在发起异步读写请求时，需要传入数据缓冲区的地址（用来存放结果数据）等信息，这样系统内核才可以自动帮我们把数据的读写工作完成，这里的读写工作全程由操作系统来做，并不需要像 Reactor 那样还需要应用进程主动发起 read/write 来读写数据，操作系统完成读写工作后，就会通知应用进程直接处理数据。
>
关于就绪、已完成 的解释：
> **就绪** 就是 准备好了，你可以去指定的地方（内核空间）把数据拿回家（用户空间）了；
> **已完成** 就是数据不但准备好了，内核还把数据送到家门口了（用户空间）。
> 
因此，Reactor 可以理解为<span style="color:red; font-weight:bold"> 来了事件操作系统通知应用进程，让应用进程来处理 </span>；而 Proactor 可以理解为<span style="color:red;  font-weight:bold"> 来了事件操作系统来处理，处理完再通知应用进程 </span>。这里的「事件」指的是：有新连接、有数据可读、有数据可写的这些 I/O 事件这里的「处理」包含从驱动读取到内核以及从内核读取到用户空间。
**或者说：**
> reactor: 提醒你这个fd有数据了
> proactor: 分离器把数据从fd缓冲区拷贝到你指定的区域，并通知你
> 
举个实际生活中的例子就是：
> Reactor 模式就是快递员在楼下，给你打电话告诉你快递到你家小区了，你需要自己下楼来拿快递;
> 而在 Proactor 模式下，快递员直接将快递送到你家门口，然后通知你。
> 

## 2. Reactor 和 Proactor 哪种是基于事件分发的？
&emsp;&emsp; 无论是 Reactor，还是 Proactor，都是一种基于「事件分发」的网络编程模式，区别在于 Reactor 模式是基于「待完成」的 I/O 事件，而 Proactor 模式则是基于「已完成」的 I/O 事件。

&emsp;
## 2. 工作中应该 使用 Reactor 还是 Proactor ？
&emsp;&emsp; Proactor模式提供了一个很好的思路，但是可惜的是，在 Linux 下的异步 I/O 是不完善的， aio 系列函数是由 POSIX 定义的异步操作接口，不是真正的操作系统级别支持的，而是在用户空间模拟出来的异步，并且仅仅支持基于本地文件的 aio 异步操作，网络编程中的 socket 是不支持的，这也使得基于 Linux 的高性能网络程序都是使用 Reactor 方案。
&emsp;&emsp; 而 Windows 里实现了一套完整的支持 socket 的异步编程接口，这套接口就是 IOCP，是由操作系统级别实现的异步 I/O，真正意义上异步 I/O，因此在 Windows 里实现高性能网络程序可以使用效率更高的 Proactor 方案。







&emsp;
&emsp;
&emsp;
# 四 总结
**(1) 常见的 Reactor 实现方案有三种。**
* &emsp;&emsp; 第一种方案单 Reactor 单进程 / 线程，不用考虑进程间通信以及数据同步的问题，因此实现起来比较简单，这种方案的缺陷在于无法充分利用多核 CPU，而且处理业务逻辑的时间不能太长，否则会延迟响应，所以不适用于计算机密集型的场景，适用于业务处理快速的场景，比如 Redis 采用的是单 Reactor 单进程的方案。
* &emsp;&emsp; 第二种方案单 Reactor 多线程，通过多线程的方式解决了方案一的缺陷，但它离高并发还差一点距离，差在只有一个 Reactor 对象来承担所有事件的监听和响应，而且只在主线程中运行，在面对瞬间高并发的场景时，容易成为性能的瓶颈的地方。
* &emsp;&emsp; 第三种方案多 Reactor 多进程 / 线程，通过多个 Reactor 来解决了方案二的缺陷，主 Reactor 只负责监听事件，响应事件的工作交给了从 Reactor，Netty 和 Memcache 都采用了「多 Reactor 多线程」的方案，Nginx 则采用了类似于 「多 Reactor 多进程」的方案。

**(2) Reactor 可以理解为「来了事件操作系统通知应用进程，让应用进程来处理」，而 Proactor 可以理解为「来了事件操作系统来处理，处理完再通知应用进程」**






&emsp;
&emsp;
&emsp;
# 参考文献
1. [如何深刻理解Reactor和Proactor？](https://www.zhihu.com/question/26943938)
2. [Reactor 模式简介](https://lotabout.me/2018/reactor-pattern/)