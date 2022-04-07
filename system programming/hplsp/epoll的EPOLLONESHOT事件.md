# 1. 为什么需要 EPOLLONESHOT ？
&emsp;&emsp; 在多线程（或进程，下同）场景下，一个线程 在读取完某个`socket`上的数据后开始处理这些数据，而在数据的处理过程中该`socket`上又有新数据可读（EPOLLIN再次被触发），此时另外一个线程被唤醒来读取这些新的数据。 这造成一个很严重的问题：
> 不同的线程在处理同一个`socket`事件，这会使程序的健壮性大降低而编程的复杂度大大增加，即使在ET模式下也有可能出现这种情况。
> 
这当然不是我们期望的。 我们期望的是一个`socket`连接在任一时刻都只被一个线程处理。 这一点可以使用`epoll`的`EPOLLONESHOT`事件实现。



&emsp;
&emsp;
# 2. EPOLLONESHOT 的作用是？
&emsp;&emsp; `EPOLLONESHOT`的作用是 确保一个`socket`连接 在任一时刻 只被一个线程处理。



&emsp;
&emsp;
# 3. 使用 EPOLLONESHOT 时需要注意什么？
&emsp; 当注册了`EPOLLONESHOT`事件的`socket`一旦被某个线程处理完毕，该线程就应该立即重置这个socket上的`EPOLLONESHOT`事件，原因如下：
> &emsp;&emsp; 对于注册了`EPOLLONESHOT`事件的文件描述符，操作系统最多触发其上注册的一个可读、可写或者异常事件，且只触发一次，除非我们使用`epoll_ctl`函数重置该文件描述符上注册的`EPOLLONESHOT`事件。因此，当一个线程在处理某个`socket`时，其他线程是不可能有机会操作该`socket`的。 
> &emsp;&emsp; 但反过来思考，注册了`EPOLLONESHOT`事件的`socket`一旦被某个线程处理完毕，该线程就应该立即重置这个socket上的`EPOLLONESHOT`事件，以确保这个`socket`下一次可读时，其EPOLLIN事件能被触发，进而让其他工作线程有机会继续处理这个`socket`。
> 
另外，listen套接字是不能 注册EPOLLONESHOT事件的，否则应用程序只能处理一个客户连接！因为后续的客户连接请求将不再触发listenfd上的EPOLLIN事件。



&emsp;
&emsp;
# 4. 为什么ET模式可能会出现两个进程(线程)读取同一个fd的情况？
&emsp; `ET`模式可能会出现两个进程(线程)读取同一个fd的情况的原因如下：
> &emsp;&emsp; ① 在`ET`模式中，会利用循环来尽量多读取数据，也就是说在处理完读取到数据之后将进行新一轮的循环，直到出现了`EAGAIN`错误；
> &emsp;&emsp; ② 新数据在该进程处理数据期间到达（`EPOLLIN`再次被触发），因此他在处理完上一次读到的数据后，继续进行新一轮的循环，尝试读取数据，此时发现还有数据没读完，那他会继续尝试从该`socket`取数据；
> &emsp;&emsp; ③ 然而此时OS唤起了另一个进程(线程)处理新到的数据（`EPOLLIN`再次被触发），此时就出现了两个进程(线程)处理同一个socket的局面。
> 



&emsp;
&emsp;
# 6. EPOLLONESHOT 是怎么做到避免两个进程同时读同一个fd的？
&emsp;&emsp; 因为设置了`EPOLLONESHOT`事件之后，OS最多触发其上注册的一个可读写或者异常事件，且只触发一次，除非使用`epoll_ctl`重置该`fd`上注册的`EPOLLONESHOT`事件。
&emsp;&emsp; 也就是说即使数据在进程处理数据的时候到达，`OS`压根不会触发新的读写就绪事件，这就意味着当前处理进程(线程)可以一直独享该fd，直到遇到`EAGAIN`错误(读完所有数据)。



&emsp;
&emsp;
# 7. 既然注册了`EPOLLONESHOT`的socket只能被一个线程(进程)处理，那这是不是会限制多线程(进程)的性能？
在使用`EPOLLONESHOT`事件的时候，如果不小心，的确会限制多线程(进程)的性能：
> &emsp;&emsp; 如果注册了`EPOLLONESHOT`事件的`socket`被某个线程处理完毕时，没有及时的重置这个socket上的`EPOLLONESHOT`事件，那么这个`socket`就不能被其它线程处理，这确实会导致多线程性能的降低。
> 
因此，使用`EPOLLONESHOT`事件的时候，千万要记住：
> &emsp;&emsp; 注册了`EPOLLONESHOT`事件的`socket`一旦被某个线程处理完毕，该线程就应该立即重置这个socket上的`EPOLLONESHOT`事件，以确保这个`socket`下一次可读时，其EPOLLIN事件能被触发，进而让其他工作线程有机会继续处理这个`socket`。
> 

