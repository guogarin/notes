# 什么是`NTPL`？
NPTL(Native POSIX Threads Library)，这是 Linux 线程实现的现代版，Linux 2.6之后的都是NTPL线程。它的前任是`LinuxThreads`，是最初的 Linux 线程实现，先已被`NTPL`取代。

设计 NPTL 是为了弥补 `LinuxThreads` 的大部分的缺陷。特别是如下部分：
> NPTL 更接近 SUSv3 Pthreads 标准。
> 使用 NPTL 的有大量线程的应用程序的性能要远优于 LinuxThreads。
> 

