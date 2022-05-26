- [1. 概述](#1-概述)
- [2. 详解](#2-详解)
  - [2.1 which](#21-which)
    - [2.2.1  原理](#221--原理)
    - [2.1.2 适用场合](#212-适用场合)
  - [2.2 whereis](#22-whereis)
    - [2.2.1 语法](#221-语法)
    - [2.2.2  查找原理](#222--查找原理)
  - [2.3 locate](#23-locate)
    - [2.3.2 查找原理](#232-查找原理)
    - [2.3.3 报错：locate: can not stat () `/var/lib/mlocate/mlocate.db‘: No such file or directory](#233-报错locate-can-not-stat--varlibmlocatemlocatedb-no-such-file-or-directory)
    - [2.3.4 十分钟前新建的文件用`locate`找不到了，为什么？](#234-十分钟前新建的文件用locate找不到了为什么)
  - [2.4 find 命令](#24-find-命令)
    - [2.4.1 语法](#241-语法)
    - [2.4.2 查找原理](#242-查找原理)
    - [2.4.3 注意事项](#243-注意事项)
    - [2.4.4 实例](#244-实例)
- [3. 总结](#3-总结)



&emsp;
&emsp;
&emsp;
## 1. 概述
我们常用的文件查找命令有下面几个：
```shell
which       # 查看可执行文件的位置。
whereis     # 查看文件的位置。
locate      # 配合数据库查看文件位置。
find        # 实际搜寻硬盘查询文件名称。
```



&emsp;
&emsp;
&emsp;
## 2. 详解
### 2.1 which
#### 2.2.1  原理
&emsp;`which` 从环境变量`PATH`中，定位和返回 与指定名字相匹配的 可执行文件 所在的路径。
情况一：找到了：
```bash
which cd    # 查找 cd命令在哪
```
结果：
> /usr/bin/cd
> 

情况二：没找到
```bash
which wwww    # 查找 wwww命令在哪
```
显然系统没有`www`这个命令，因此结果为：
> /usr/bin/which: no www in (/home/zhangsan/opt/mysql/bin/:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/zhangsan/.local/bin:/home/zhangsan/bin)
> 

#### 2.1.2 适用场合
&emsp;&emsp; 一般用于查找 命令 和 可执行文件 所在的路径。有时候可能在多个路径下存在相同的命令，该命令可用于查找当前所执行的命令到底是哪一个位置处的命令。

&emsp;
### 2.2 whereis
#### 2.2.1 语法
`whereis`命令只能用来查找二进制文件、源代码文件、说明文件(帮助文档):
```bash
whereis [-bfmsu][-B <目录>...][-M <目录>...][-S <目录>...][文件...]
```
> `-b` 　: 只查找二进制文件。
> `-B<目录>` 　: 　只在设置的目录下查找二进制文件。
> `-f` 　: 　不显示文件名前的路径名称。
> `-m` 　: 　只查找说明文件。
> `-M<目录>` 　: 　只在设置的目录下查找说明文件。
> `-s` 　: 　只查找源文件文件。
> `-S<目录>` 　: 　只在设置的目录下查找原始代码文件。
> `-u` 　: 　查找不包含指定类型的文件。
> 
若省略参数则返回所有的信息。
#### 2.2.2  查找原理
```bash
[alpha@localhost ~]$ whereis -b ls # 查找ls命令的 二进制文件 所在的文件夹
ls: /usr/bin/ls
[alpha@localhost ~]$ whereis -s ls # 查找ls命令的 源文件 所在的文件夹
ls:
[alpha@localhost ~]$ whereis -m ls #  查找ls命令的 帮助文档 所在的文件夹
ls: /usr/share/man/man1/ls.1.gz /usr/share/man/man1p/ls.1p.gz
```

&emsp;
### 2.3 locate

#### 2.3.2 查找原理
`locate`是借助数据库`/var/lib/mlocate/`来进行查找的。

#### 2.3.3 报错：locate: can not stat () `/var/lib/mlocate/mlocate.db‘: No such file or directory
问题如下：
```bash
[alpha@localhost ~]$ locate code/
locate: can not stat () `/var/lib/mlocate/mlocate.db': No such file or directory
```
出现这个问题是因为安装了`locate`命令后，没有进行`update`数据库，需要运行`updatedb`命令来更新以下数据库：
```bash
[alpha@localhost ~]$ updatedb
updatedb: can not open a temporary file for `/var/lib/mlocate/mlocate.db'
```
没有权限，`su`到`root`用户再试试：
```bash
[root@localhost alpha] updatedb # 更新数据库
[root@localhost alpha] locate overrive.cpp
/home/alpha/code/cpp_primer/overrive.cpp
```
可以看到，在切到`root`用户后成功的更新了数据库，并用`locate`查到了`overrive.cpp`文件的所在之处。

#### 2.3.4 十分钟前新建的文件用`locate`找不到了，为什么？
**(1) 问题：**
```bash
[root@localhost alpha] touch just_for_test.py

[root@localhost alpha] ll
-rw-r--r--. 1 root  root   0 May 26 19:48 just_for_test.py

[root@localhost alpha] locate just_for_test.py 
# 结果为空，locate命令没找到 just_for_test.py
```
**(2) 原因：**
&emsp;&emsp; `locate`命令是由数据库来查找的，而数据库的建立默认是每天执行一次，所以新建立文件 在更新数据库之前是找不到了。
**(3) 解决：** 
&emsp;&emsp; 用到updatedb命令更新数据库即可：
```bash
[root@localhost alpha] updatedb 
[root@localhost alpha] locate just_for_test.py 
/home/alpha/just_for_test.py
```
可以看到的是，在更新数据库后，文件就能通过`locate`命令找到了。

&emsp;
### 2.4 find 命令
#### 2.4.1 语法
```bash
find   path   -option   [   -print ]   [ -exec   -ok   command ]   {} \;
```
> -mount, -xdev : 只检查和指定目录在同一个文件系统下的文件，避免列出其它文件系统中的文件
> 
> -amin n : 在过去 n 分钟内被读取过
> 
> -anewer file : 比文件 file 更晚被读取过的文件
> 
> -atime n : 在过去 n 天内被读取过的文件
> 
> -cmin n : 在过去 n 分钟内被修改过
> 
> -cnewer file :比文件 file 更新的文件
> 
> -ctime n : 在过去 n 天内创建的文件
> 
> -mtime n : 在过去 n 天内修改过的文件
> 
> -empty : 空的文件-gid n or -group name : gid 是 n 或是 group 名称是 name
> 
> -ipath p, -path p : 路径名称符合 p 的文件，ipath 会忽略大小写
> 
> -name name, -iname name : 文件名称符合 name 的文件。iname 会忽略大小写
> 
> -size n : 文件大小 是 n 单位，b 代表 512 位元组的区块，c 表示字元数，k 表示 kilo bytes，w 是二个位元组。
> 
> -type c : 文件类型是 c 的文件。
> > > d: 目录
> > > c: 字型装置文件
> > > b: 区块装置文件
> > > p: 具名贮列
> > > f: 一般文件
> > > l: 符号连结
> > > s: socket
> > > -pid n : process id 是 n 的文件
>

#### 2.4.2 查找原理
&emsp;&emsp; 遍历当前工作目录及其子目录，find命令是在硬盘上遍历查找，非常耗硬盘资源，查找效率相比whereis和locate较低。

#### 2.4.3 注意事项
&emsp;&emsp; `find`命令是在硬盘上遍历查找，非常耗硬盘资源，查找效率较低，能用`which`、`whereis`和`locate`的时候尽量不要用`find`.

#### 2.4.4 实例
(1) 将当前目录及其子目录下所有文件后缀为 `.c` 的文件列出来:
```bash
find . -name "*.c"
```

(2) 将当前目录及其子目录中的所有文件列出：
```bash
find . -type f
```

(3) 将当前目录及其子目录下所有最近 `20` 天内更新过的文件列出:
```bash
find . -ctime  20
```

(4) 查找 `/var/log` 目录中更改时间在 `7` 日以前的普通文件，并在删除之前询问它们：
```bash
find /var/log -type f -mtime +7 -ok rm {} \;
```

(5) 查找当前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件：
```bash
find . -type f -perm 644 -exec ls -l {} \;
```

(6)查找系统中所有文件长度为 `0` 的普通文件，并列出它们的完整路径：
```bash
find / -type f -size 0 -exec ls -l {} \;
```



&emsp;
&emsp;
&emsp;
## 3. 总结
下图来自于网络：
<div align="center"> <img src="./pic/linux查找命令比较.png"> </div>
<center> <font color=black> <b> 图3 linux查找命令比较 </b> </font> </center>

