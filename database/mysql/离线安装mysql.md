# 1. 下载安装包
网站：<https://downloads.mysql.com/archives/community/>
选择的版本：MySQL Community Server (mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz)
**mysql的各种版本**：
> 1.rpm package:是某个特定的包，比如server,client,shared lib等  -- 是的，可以单独安装
  2、rpm bundle：是该版本所有包的集合                           --- 一般是把服务器端要用的都安装上，其他的不带，尤其是开发包
  3、Compressed TAR Archive，是源码，必须用源码方式安装。       ----  这个是源码，需要自己编译的，也有编译好，但不是安装包的



&emsp;
# 2. 解压文件
(1) mysql下载完成后上传到当前普通用户目录下解压，依次执行以下命令:
```shell
mkdir opt
mkdir opt/mysql
tar -zxvf mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz -C opt/ # 将mysql安装包解压到 opt文件夹下
cd opt/
mv mysql-5.7.31-linux-glibc2.12-x86_64/ mysql   # 重命名
```
(2) 编辑`my.cnf`配置文件，放在当前mysql安装目录下，依次执行以下命令
```shell
cd mysql	      #进入安装目录
vim my.cnf	      #编辑配置文件
```
加入如下内容：
```shell
[client]
port=3306					#服务端口
socket=/home/zhangsan/opt/mysql/mysql.sock		#指定套接字文件

[mysqld]
port=3306					#服务端口
basedir=/home/zhangsan/opt/mysql			#mysql安装路径
datadir=/home/zhangsan/opt/mysql/data                   #数据目录
pid-file=/home/zhangsan/opt/mysql/mysql.pid		#指定pid文件
socket=/home/zhangsan/opt/mysql/mysql.sock		#指定套接字文件
log_error=/home/zhangsan/opt/mysql/error.log            #指定错误日志
server-id=100                                   #Mysql主从唯一标识
```
**注：** 此处为了方便，直接将220上的配置文件拿过来修改了路径就用了


&emsp;
# 3. 安装启动mysql
## 3.1 安装
```shell
cd bin
./mysqld --defaults-file=/home/zhangsan/opt/mysql/my.cnf --initialize --user=zhangsan --basedir=/home/zhangsan/opt/mysql --datadir=/home/zhangsan/opt/mysql/data		#安装并初始化mysql

```
## 3.2 启动
依次执行以下命令，
```shell
./mysqld_safe --defaults-file=/home/zhangsan/opt/mysql/my.cnf --user=zhangsan &
```
没有报错并能成功监听3306端口即表示启动成功
```shell
netstat -tln | grep 3306
```
## 错误排查
&emsp;&emsp; 若未能正常启动，可以看 `error.log ` ，错误信息都在里面，文件路径就是在 `my.cnf` 里的那个。


&emsp;
# 4. 登入mysql
## 4.1 获取初始密码
&emsp;&emsp; 初始密码在初始化的时候生成，写在了 `error.log` 里，所以要到日志里找：
```shell
less error.log | grep root@localhost		#查找root用户的初始登录密码
```
## 4.2 登录mysql
输入命令：
```shell
bin/mysql -u root -p
```
输入密码后报错了：
> ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)

此时我们应该指定sock登录：
```shell
ps -ef | grep mysql # 找出 mysql 对应 sock文件
bin/mysql -u root -p --socket=/home/zhangsan/opt/mysql/mysqld.sock # 指定 sock登录
```
最后输入密码，成功登录。
## 4.3 修改root密码
```sql
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');	--设置登录密码为123456
flush privileges; -- mysql 新设置用户或更改密码后需用flush privileges刷新MySQL的系统权限相关表，否则会出现拒绝访问
```


&emsp;
# 5. 开启远程访问
## 5.1 查看现有访问权限
&emsp;&emsp; 使用第三方工具连接mysql数据库时，需要提前开启mysql的远程访问限制，可以执行以下sql语句查看访问权限：
```sql
use mysql;		                                          --切换至mysql数据库
select User, authentication_string, Host from user;		--查看用户认证信息
```
输入上述sql语句后可以看到都是localhost的访问权限：
```
    +---------------+-------------------------------------------+-----------+
    | User          | authentication_string                     | Host      |
    +---------------+-------------------------------------------+-----------+
    | root          | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 | localhost |
    | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | localhost |
    | mysql.sys     | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | localhost |
    +---------------+-------------------------------------------+-----------+
```
## 5.2 修改权限


允许其他地址的主机访问mysql，这里密码是123456，实际根据自己的来，`%` 代表所有主机，也可以具体到ip地址:
```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456';
flush privileges; -- mysql 新设置用户或更改密码后需用flush privileges刷新MySQL的系统权限相关表，否则会出现拒绝访问
```


## 5.3 验证
&emsp;&emsp; 再次查表我们可以发现多了个用户，表示成功开启远程访问，可以使用工具远程连接：
```sql		                                          --切换至mysql数据库
select User, authentication_string, Host from user;		--查看用户认证信息
```
结果：
```
    +---------------+-------------------------------------------+-----------+
    | User          | authentication_string                     | Host      |
    +---------------+-------------------------------------------+-----------+
    | root          | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 | localhost |
    | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | localhost |
    | mysql.sys     | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | localhost |
    | root          | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 | %         |
    +---------------+-------------------------------------------+-----------+
```



# 6. 参考文献：
1.[Linux普通用户安装配置mysql（非root权限）](https://www.cnblogs.com/chenkx6/p/13366638.html?utm_source=tuicool)