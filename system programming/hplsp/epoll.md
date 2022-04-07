# 1. `epoll`是什么？
&emsp; `epoll`是Linux特有的I/O复用函数。它在实现和使用上与`select`、 `poll`有很大差异，换句话说，它有如下特点：
> &emsp;&emsp; ① Linux特有；
> &emsp;&emsp; ② 和`select`一样，它是一个IO复用函数。
> 

# 2. 相比于其它IO复用函数(`select`、 `poll`)，`epoll`有什么特点？
&emsp; 第一， `epoll`使用一组函数来完成任务， 而不是单个函数。 
&emsp; 第二， `epoll`把用户关心的文件描述符上的事件放在内核里的一个事件表中， 从而无须像select和poll那样每次调用都要重复传入文件描述符集或事件集。 
&emsp; 第三，`epoll`对文件描述符的操作有两种模式：LT（Level Trigger，水平触发）模式和ET（Edge Trigger，边沿触发）模式。
> &emsp;&emsp; LT模式是默认的工作模式， 这种模式下`epoll`相当于一个效率较高的`poll`。 
> &emsp;&emsp; 当往`epoll`内核事件表中注册一个文件描述符上的`EPOLLET`事件时， `epoll`将以`ET`模式来操作该文件描述符。 `ET`模式是`epoll`的高效工作模式。
> 
这两种工作模式的差异如下：
> &emsp;&emsp; 对于**采用LT工作模式**的文件描述符， 当`epoll_wait`检测到其上有事件发生并将此事件通知应用程序后，应用程序可以不立即处理该事件。 这样， 当应用程序下一次调用`epoll_wait`时， `epoll_wait`还会再次向应用程序通告此事件， 直到该事件被处理。
> &emsp;&emsp; 而对于**采用ET工作模式**的文件描述符， 当`epoll_wait`检测到其上有事件发生并将此事件通知应用程序后， 应用程序必须立即处理该事件， 因为后续的`epoll_wait`调用将不再向应用程序通知这一事件。 
> 
显然， ET模式在很大程度上降低了同一个epoll事件被重复触发的次数， 因此效率要比LT模式高。 

# 3. `epoll`的基本上使用
(1) `epoll`需要使用一个额外的文件描述符，来唯一标识内核中的这个事件表。 这个文件描述符使用如下`epoll_create`函数来创建：
```cpp
#include <sys/epoll.h>

// size参数现在并不起作用， 只是给内核一个提示， 告诉它事件表需要多大。 
// 该函数返回的文件描述符将用作其他所有epoll系统调用的第一个参数， 以指定要访问的内核事件表。
int epoll_create(int size)
```
(2) 下面的函数用来操作`epoll`的内核事件表：
```cpp
#include <sys/epoll.h>

// epoll_ctl成功时返回0， 失败则返回-1并设置errno。
int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event)
```
`fd`参数是要操作的文件描述符， `op`参数则指定操作类型。 操作类型有如下3种：
> ❑ `EPOLL_CTL_ADD`， 往事件表中注册`fd`上的事件。
> ❑ `EPOLL_CTL_MOD`， 修改`fd`上的注册事件。
> ❑ `EPOLL_CTL_DEL`， 删除`fd`上的注册事件。
> 
`event`参数指定事件， 它是`epoll_event`结构指针类型。 `epoll_event`的定义如下：
```cpp
struct epoll_event
{
    __uint32_t events;/*epoll事件*/
    epoll_data_t data;/*用户数据*/
};
```
其中`events`成员描述事件类型。 `epoll`支持的事件类型和poll基本相同。 表示`epoll`事件类型的宏是在poll对应的宏前加上“`E`”， 比如`epoll`的数据可读事件是`EPOLLIN`。 但`epoll`有两个额外的事件类型——`EPOLLET`和`EPOLLONESHOT`。 它们对于`epoll`的高效运作非常关键， 我们将在后面讨论它们。 `data`成员用于存储用户数据， 其类型`epoll_data_t`的定义如下：
```cpp
// 注意它是一个union，不是一个struct
typedef union epoll_data
{
    void* ptr;
    int fd;
    uint32_t u32;
    uint64_t u64;
}epoll_data_t;
```
`epoll_data_t`是一个联合体， 其`4`个成员中使用最多的是`fd`， 它指定事件所从属的目标文件描述符。 `ptr`成员可用来指定与`fd`相关的用户数据。但由于`epoll_data_t`是一个联合体， 我们不能同时使用其`ptr`成员和`fd`成员， 因此，如果要将文件描述符和用户数据关联起来（正如8.5.2小节讨论的将句柄和事件处理器绑定一样），以实现快速的数据访问，只能使用其他手段，比如放弃使用`epoll_data_t`的fd成员，而在`ptr`指向的用户数据中包含`fd`。

(3) `epoll_wait`函数
&emsp;&emsp; `epoll`系列系统调用的主要接口是`epoll_wait`函数。 它在一段超时时间内等待一组文件描述符上的事件，其原型如下：
```cpp
#include <sys/epoll.h>

// 成功时返回就绪的文件描述符的个数， 失败时返回-1并设置errno。
int epoll_wait(int epfd,struct epoll_event*events, int maxevents, int timeout);
```
`timeout`参数的含义与poll接口的timeout参数相同。 `maxevents`参数指定最多监听多少个事件， 它必须大于0。
epoll_wait函数如果检测到事件， 就将所有就绪的事件从内核事件表（由epfd参数指定） 中复制到它的第二个参数events指向的数组中。 这个数组只用于输出epoll_wait检测到的就绪事件， 而不像select和poll的数组参数那样既用于传入用户注册的事件， 又用于输出内核检测到的就绪事件。 这就极大地提高了应用程序索引就绪文件描述符的效率。 代码清单9-2体现了这个差别。
```cpp
/*如何索引poll返回的就绪文件描述符*/
int ret=poll(fds, MAX_EVENT_NUMBER, -1);
/*必须遍历所有已注册文件描述符并找到其中的就绪者（当然， 可以利用ret来稍做优化） */
for(int i=0; i＜MAX_EVENT_NUMBER; ++i){
    if(fds[i].revents＆POLLIN){/*判断第i个文件描述符是否就绪*/
        int sockfd=fds[i].fd;
        /*处理sockfd*/
    }
}

/*如何索引epoll返回的就绪文件描述符*/
int ret = epoll_wait(epollfd, events, MAX_EVENT_NUMBER, -1);
/*仅遍历就绪的ret个文件描述符*/
for(int i=0;i＜ret;i++){
    int sockfd=events[i].data.fd;
    /*sockfd肯定就绪， 直接处理*/
}
```
