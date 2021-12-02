## 1. 查看文件夹大小
`du -sh` 
-h或--human-readable    以K，M，G为单位，提高信息的可读性。
-s或--summarize 仅      显示总计。



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