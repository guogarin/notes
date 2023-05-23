[toc]




## 1. 查看文件夹大小
`du -sh` 
① `du` 是 Disk Usage 的缩写
② `-h` 或 --human-readable    以K，M，G为单位，提高信息的可读性。
③ `-s` 或 --summarize 仅      显示总计。



&emsp;&emsp; 
&emsp;&emsp; 
## 2. 定时任务：crontab
### 2.1 语法
```shell
crontab [ -u user ] file

# 或者
crontab [ -u user ] { -l | -r | -e }
```
参数说明：
* `-u user` 是指设定指定 user 的时程表，这个前提是你必须要有其权限(比如说是 root)才能够指定他人的时程表。如果不使用 -u user 的话，就是表示设定自己的时程表。
* `-e` : 执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，则请先设定 VISUAL 环境变数来指定使用那个文字编辑器(比如说 setenv VISUAL joe)
* `-r` : 删除目前的时程表
* `-l` : 列出目前的时程表
* 
### 2.2 时间格式
时间格式如下：
```
f1 f2 f3 f4 f5 program
```
&emsp;&emsp; 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
&emsp;&emsp; 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推
&emsp;&emsp; 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
&emsp;&emsp; 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
&emsp;&emsp; 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推
```
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```
### 2.3 实例
(1) 每一分钟执行一次 `/bin/ls`：
```shell
* * * * * /bin/ls
```
(2) 在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 `/usr/bin/backup`：
```shell
0 6-12/3 * 12 * /usr/bin/backup
```
(3) 周一到周五每天下午 5:00 寄一封信给 alex@domain.name：
```shell
0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata
```
(4) 每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 `echo "haha"`：
```shell
20 0-23/2 * * * echo "haha"
```
(5) 几个具体的例子：
```shell
0 */2 * * * /sbin/service httpd restart  # 每两个小时重启一次apache 

50 7 * * * /sbin/service sshd start  # 每天7：50开启ssh服务 

50 22 * * * /sbin/service sshd stop  # 每天22：50关闭ssh服务 

0 0 1,15 * * fsck /home  #每月1号和15号检查/home 磁盘 

1 * * * * /home/bruce/backup  #每小时的第一分执行 /home/bruce/backup这个文件 

00 03 * * 1-5 find /home "*.xxx" -mtime +4 -exec rm {} \;  # 每周一至周五3点钟，在目录/home中，查找文件名为*.xxx的文件，并删除4天前的文件。

30 6 */10 * * ls  # 每月的1、11、21、31日是的6：30执行一次ls命令
```
**注意：** 当程序在你所指定的时间执行后，系统会发一封邮件给当前的用户，显示该程序执行的内容，若是你不希望收到这样的邮件，请在每一行空一格之后加上 `> /dev/null 2>&1` 即可，如：
```shell
20 03 * * * . /etc/profile;/bin/sh /var/www/runoob/test.sh > /dev/null 2>&1 
```
### 2.4 备份数据库时，数据库文件为空
&emsp;&emsp; 直接执行备份用的shell文件`mysql_backup.sh`时可以正常备份数据库，但是`crontab`定时执行生成的备份文件就为空了，经过查找，原因就是可执行文件`mysql_backup.sh`中`mysqldump`的命令没有写绝对路径导致的，因为直接执行时是在`mysql`的`bin`目录下执行的，所以没有问题，但是`cronta`b就不是在`mysql`的`bin`下了，所以找不到`mysqldump`的命令了。

### 2.5 参考文件
(1) [Linux crontab 命令](https://www.runoob.com/linux/linux-comm-crontab.html)
(2) [linux定时备份mysql数据库，及解决crontab执行时生成数据库文件为空的问题](https://www.cnblogs.com/wanghaitao/p/9440332.html)



&emsp;&emsp; 
&emsp;&emsp; 
## 3. `kill`和`kill -9`
### 3.1  它们有何区别？
它们发送的信号不一样：
> `kill`发送的是`SIGTERM`信号
> `kill -9`发送的是`SIGKILL`信号
> 
所以`kill`和`kill -9`的区别可以理解为`SIGTERM`和`SIGKILL`的区别：
> `SIGTERM`和`SIGKILL`都是用来终止进程的，但是它们之间的区别的主要是能不能被信号处理函数阻塞：
> &emsp;&emsp; `SIGTERM`是可以被信号处理器阻塞的，如果进程定义了自己的信号处理函数，那么完全可以不杀死自己；
> &emsp;&emsp; `SIGKILL`是必杀信号，信号处理器也不能将其阻塞。
> 
### 3.2 平常用哪个比较好？
&emsp;&emsp; 正常情况下建议使用`kill`，因为它发送的`SIGTERM`信号会先调用信号处理程序，而且在信号处理程序中可能有一些资源的清理工作，这样可以避免资源泄露的风险。
&emsp;&emsp; 如果实在杀不掉，再调用`kill -9`。



&emsp;&emsp; 
&emsp;&emsp; 
## 4. 包管理工具
### 4.1 Linux有哪几种常见的包格式？
Linux系统中有两种最常见的安装包格式：
> ① rpm包 
> ② deb包
> 
rpm包 主要应用在RedHat系列，包括 Fedora、centOS等
deb包 主要应用于Debian系列，包括现在比较流行的Ubuntu等发行版上。 

### 4.2  `rpm`
**首先，要区分rpm包和rpm命令**
在linux中，rpm有两个意思：
> ① 在红帽子中，安装包的格式(如`xxx.rpm`)；
> ② 红帽子中，管理rpm包的命令(工具)
> 
比如，下面这个命令：
```bash
rpm -hvi dejagnu-1.4.2-10.noarch.rpm 
```
就是在安装`dejagnu-1.4.2-10.noarch.rpm`包

### 4.3 `yum`
#### 4.4 简介
&emsp;&emsp; 由于Linux中的程序大多是小程序。程序与程序之间存在非常复杂的依赖关系。RPM无法解决软件包的依赖关系。
&emsp;&emsp; `Yum`（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。
&emsp;&emsp; 也就是说，`yum`基于`rpm`，算是对其进行了一层封装，提高了用户友好度。

#### 4.5 使用
**yum 语法**
```bash
yum [options] [command] [package ...]
```
> `options` ：可选，选项包括`-h`（帮助），`-y`（当安装过程提示选择全部为 "yes"），`-q`（不显示安装的过程）等等。
> `command` ：要进行的操作。
> `package` ：安装的包名。
> 


### 4.4 `apt-get`
&emsp;&emsp; `apt-get`也是一个包管理工具，属于ubuntu、Debian。



&emsp;&emsp; 
&emsp;&emsp; 
## 5. 网络编程中常用的命令和工具
### 5.1 网络编程有哪些常用的命令？
| 命令      | 对应英文       | 作用 |
| --------- | -------------- | ---- |
| `tcpdump` |                |      |
| `nc`      |                |      |
| `strace`  |                |      |
| `lsof`    | list open file |      |
| `netstat` |                |      |
| `vmstat`  |                |      |
| `ifstat`  |                |      |
| `mpstat`  |                |      |

### netstat
&emsp;&emsp; `netstat`是一个功能很强大的网络信息统计工具。 它可以打印本地网卡接口上的全部连接、 路由表信息、 网卡接口信息等。
`netstat`命令常用的选项包括：
| 选项                       | 作用                                       |
| -------------------------- | ------------------------------------------ |
| -a 或–all                  | 显示连接的套接字(包括 LISTEN、ESTABLISHED) |
| -A <网络类型>或–<网络类型> | 列出该网络类型连线中的相关地址。           |
| -c 或–continuous           | 每隔1 s输出一次                            |
| -C 或–cache                | 显示路由器配置的快取信息。                 |
| -e 或–extend               | 显示网络其他相关信息。                     |
| -F 或–fib                  | 显示FIB。                                  |
| -g 或–groups               | 显示多重广播功能群组组员名单。             |
| -h 或–help                 | 在线帮助。                                 |
| -i 或–interfaces           | 显示网络界面信息表单。                     |
| -l 或–listening            | 显示LISTEN状态的服务器的Socket。           |
| -M 或–masquerade           | 显示伪装的网络连线。                       |
| -n 或–numeric              | 直接使用IP地址，而不通过域名服务器。       |
| -N 或–netlink或–symbolic   | 显示网络硬件外围设备的符号连接名称。       |
| -o 或–timers               | 显示socket定时器（比如保活定时器） 的信息  |
| -p 或–programs             | 显示socket所属的进程的PID和名字            |
| -r 或–route                | 显示Routing Table。                        |
| -s 或–statistice           | 显示网络工作信息统计表。                   |
| -t 或–tcp                  | 仅显示TCP连接                              |
| -u 或–udp                  | 仅显示UDP连接                              |
| -v 或–verbose              | 显示指令执行过程。                         |
| -V 或–version              | 显示版本信息。                             |
| -w 或–raw                  | 显示RAW传输协议的连线状况。                |
| -x 或–unix                 | 此参数的效果和指定”-A unix”参数相同。      |
| –ip 或–inet                | 此参数的效果和指定”-A inet”参数相同。      |

#### 常用组合
**(1) 查看某个端口是否被占用**
```shell
netstat -nlt # -l 只显示LISTEN状态的服务
netstat -nat # -a 不仅仅显示LISTEN状态的服务，ESTABLISHED状态的也显示
```
然后搭配管道和`grep`进行筛选：
```shell
netstat -nat | grep 3306 # 查看3306端口是否被占用
```
来看看`netstat -nat`结果：
```
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp6       0      0 :::111                  :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
tcp6       0      0 ::1:631                 :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN   
```
可以看到`netstat`的每行输出都包含如下`6`个字段（默认情况） ：
|                     |                                                  |
| ------------------- | ------------------------------------------------ |
| ① `Proto`           | 协议名（tcp或udp）。                             |
| ② `Recv-Q`          | socket内核接收缓冲区中尚未被应用程序读取的数据量 |
| ③ `Send-Q`          | 未被对方确认的数据量。                           |
| ④ `Local Address`   | 本端的IP地址和端口号。                           |
| ⑤ `Foreign Address` | 对方的IP地址和端口号。                           |
| ⑥ `State`           | socket的状态。                                   |
注：
> &emsp;&emsp; 对于无状态协议，比如UDP协议，这一字段将显示为空。而对面向连接的协议而言，netstat支持的State包括`ESTABLISHED、 SYN_SENT、 SYN_RCVD、 FIN_WAIT1、 FIN_WAIT2、TIME_WAIT、CLOSE、CLOSE_WAIT、 LAST_ACK、 LISTEN、 CLOSING、 UNKNOWN`。
> 
