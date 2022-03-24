# 1. 背景
## 1.1 为什么需要非阻塞的connect
&emsp;&emsp; (1) 主要是为了提高并发度，因为TCP 连接的建立涉及到一个三次握手的过程，且 SOCKET 中 connect 函数需要一直等到客户接收到对于自己的 SYN 的 ACK 为止才返回，这意味着每个 connect 函数总会阻塞其调用进程至少一个到服务器的 RTT 时间，而 RTT 波动范围很大，从局域网的几个毫秒到几百个毫秒甚至广域网上的几秒。这段时间内，我们可以执行其他处理工作，以便做到并行。因此需要用到非阻塞 connect。
&emsp;&emsp; (2) 可以用这种技术同时建立多个连接。在 Web 浏览器中很普遍； 
&emsp;&emsp; (3) 由于我们使用 select 来等待连接的完成，因此我们可以给 select 设置一个时间限制，从而缩短 connect 的超时时间。在大多数实现中，connect 的超时时间在  75 秒到几分钟 之间。有时候应用程序想要一个更短的超时时间，使用非阻塞 connect 就是一种方法。 

## 1.2 当socket为 阻塞 和 非阻塞 时，调用connect()有何不同?
客户端调用connect()发起对服务端的socket连接，若客户端的socket fd为
> ① **阻塞模式**，则connect()会阻塞到 连接建立成功 或 连接建立超时（linux内核中对connect的超时时间限制是75s， Soliris 9是几分钟，因此通常认为是75s到几分钟不等）。
> ② **非阻塞模式**，则调用connect()后函数立即返回，如果连接不能马上建立成功（返回-1），则errno设置为EINPROGRESS，此时TCP三次握手仍在继续。此时可以调用select()检测非阻塞connect是否完成。注意：select指定的超时时间可以比connect的超时时间短，因此可以防止连接线程长时间阻塞在connect处。
> 



# 2. 非阻塞的connect
## 2.1 非阻塞connect的编写中，connect()函数的返回值 的判断规则是？
> ① 如果 返回值 等于0，说明 三次握手马上就成功了，因此就没必要进行select()了；
> ② 如果 返回值 小于0，且 errno为EINPROGRESS, 表示连接建立已经启动但是尚未完成。(这是期望的结果，不是真正的错误);
> ③ 如果 返回值 小于0，且errno 不是 EINPROGRESS，则连接出错了。
>

## 2.2 非阻塞connect的编写中，select() 的判断规则是？
非阻塞connect的编写中，select()返回值 的判断规则是？
> (1) 若select()返回值小于0，说明select()发生了错误；
> (2) 检查socket fd 是否在select()返回的写就绪事件集中；
> (3) 若 socket fd 在select()返回的写就绪事件集中，则：
> &emsp;&emsp; ① 调用 getsockopt()获取socket fd 上的错误errno；
> &emsp;&emsp; ② 若errno == 0，则说明已完成三次握手并建立了连接；
> &emsp;&emsp; ③ 若errno != 0，则说明连接发生错误。
> 

## 2.3 非阻塞connect的时候，连接成功时select(或poll)会返回什么事件？连接失败呢？
&emsp;&emsp; 注意，要用select 或 poll等函数来监听的是 可写事件！因为**连接建立了**，该socket连接的写缓冲区空闲，即该socket可写。
&emsp;&emsp; **当连接建立出错时**，套接口描述符变成  既可读又可写(有未决的错误，从而可读又可写)； 

## 2.4 代码实现
```cpp
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <assert.h>
#include <stdio.h>
#include <time.h>
#include <errno.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <string.h>

#define BUFFER_SIZE 1023


int setnonblocking( int fd )
{
    // 第一步：通过F_GETFL获取旧的文件描述符的flags参数
    int old_option = fcntl( fd, F_GETFL );
    // 第二步：在旧的flags 参数的基础上添加非阻塞标识 (O_NONBLOCK)
    int new_option = old_option | O_NONBLOCK;
    // 第三步：通过F_SETFL和new_option将socket fd改成非阻塞模式
    fcntl( fd, F_SETFL, new_option );
    // 最后，返回旧的flags参数
    return old_option;
}


int unblock_connect( const char* ip, int port, int time )
{
    int ret = 0;
    /*  ****************************************************************************************

        1. 以下是建立internet socket的过程

    *   *****************************************************************************************/    
    // Internet domain socket 地址，此处为IPV4地址，IPV6地址为 struct sockaddr_in6
    struct sockaddr_in address; 
    // bzero和memset函数功能类似，将内存清零
    bzero( &address, sizeof( address ) );
    // AF_INET表示socket类型为internet domain套接字，对应的是unix domain套接字AF_UNIX
    address.sin_family = AF_INET;
    //  p 表示“当前（ presentation）”， n 表示“网络（ network）”
    // inet_pton()负责将 IP 换成 网络字节序的二进制 IP 地址
    inet_pton( AF_INET, ip, &address.sin_addr );
    // htons其实是一个宏，和htons()、 htonl()、 ntohs()以及 ntohl()函数一样，
    // 被用来在主机和网络字节序之间转换整数。
    // htons是host to network short的缩写
    address.sin_port = htons( port );
    // 对于BSD，是AF_INET，对于POSIX是PF_INET，现在很多人都混用它们了
    int sockfd = socket( PF_INET, SOCK_STREAM, 0 );


    /*  ****************************************************************************************

        2. 将socket fd修改为非阻塞模式，然后进行connect()操作，并根据connet()的返回值进行相应的处理。

    *   *****************************************************************************************/
    // 
    int fdopt = setnonblocking( sockfd );
    ret = connect( sockfd, ( struct sockaddr* )&address, sizeof( address ) );
    // 情况一：如果 返回值 等于0，说明 三次握手马上就成功了，因此就没必要进行select()了；
    if ( ret == 0 )
    {
        printf( "connect with server immediately\n" );
        // 连接一connect()就连上了，显然不需要加入到select队列里，所以把fd的flags改为旧的模式(即阻塞)
        fcntl( sockfd, F_SETFL, fdopt );
        return sockfd;
    }
    // 情况二：errno 不是 EINPROGRESS，则连接出错了。
    else if ( errno != EINPROGRESS )
    {
        printf( "unblock connect not support\n" );
        return -1;
    }
    // 情况三：errno为EINPROGRESS, 表示连接建立已经启动但是尚未完成。(注意，这是期望的结果，不是真正的错误)
    fd_set readfds;
    fd_set writefds;
    struct timeval timeout;

    FD_ZERO( &readfds );
    FD_SET( sockfd, &writefds );

    timeout.tv_sec = time;  // 秒数
    timeout.tv_usec = 0;    // 微秒数
    // 调用select()，超时时间设为的10s，因此select会至多阻塞10s
    ret = select( sockfd + 1, NULL, &writefds, NULL, &timeout );
    // 若select()返回值小于0，说明select()发生了错误；
    if ( ret <= 0 )
    {
        printf( "connection time out\n" );
        close( sockfd );
        return -1;
    }
    // 若目标fd不在就绪事件集合writefds中，则关闭套接字，返回-1
    if ( ! FD_ISSET( sockfd, &writefds  ) )
    {
        printf( "no events on sockfd found\n" );
        close( sockfd );
        return -1;
    }
    // 若目标fd在就绪事件集合writefds中：
    //   ① 调用 getsockopt()获取socket fd 上的错误errno；
    int error = 0;
    socklen_t length = sizeof( error );
    if( getsockopt( sockfd, SOL_SOCKET, SO_ERROR, &error, &length ) < 0 )
    {
        printf( "get socket option failed\n" );
        close( sockfd );
        return -1;
    }
    //   ② 若errno != 0，则说明连接发生错误。
    if( error != 0 )
    {
        printf( "connection failed after select with the error: %d \n", error );
        close( sockfd );
        return -1;
    }
    // ③  若errno == 0，则说明已完成三次握手并建立了连接；
    printf( "connection ready after select with the socket: %d \n", sockfd );
    // 将fd改回到阻塞模式
    fcntl( sockfd, F_SETFL, fdopt );
    return sockfd;
}

int main( int argc, char* argv[] )
{
    // 通过命令行输入IP和Port
    if( argc <= 2 )
    {
        printf( "usage: %s ip_address port_number\n", basename( argv[0] ) );
        return 1;
    }
    const char* ip = argv[1];
    // ascii to integer，把字符串(这里是端口)转换成整型数字。
    int port = atoi( argv[2] ); 

    int sockfd = unblock_connect( ip, port, 10 );
    if ( sockfd < 0 )
    {
        return 1;
    }
    close(sockfd); // 关闭文件描述符
    return 0;
}
```