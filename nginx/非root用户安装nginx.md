## 1. 确定依赖
确认nginx的依赖：
&emsp; (1) ssl 功能需要 openssl 库 
&emsp; (2) gzip 模块需要 zlib 库
&emsp; (3) rewrite 模块需要 pcre 库



&emsp; 
## 2. 下载相关压缩包
```shell
wget http://nginx.org/download/nginx-1.8.0.tar.gz   
wget http://www.openssl.org/source/openssl-fips-2.0.9.tar.gz 
wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
wget  http://www.zlib.net/zlib-1.2.11.tar.gz
```



&emsp; 
## 3. 编译依赖
### 3.1 编译openssl
```shell
tar -zxvf openssl-fips-2.0.9.tar.gz
mv openssl-fips-2.0.9 openssl
cd openssl
./config --prefix=自定义用户目录
make 
make install
```
### 3.2 编译pcre
```shell
tar -zxvf pcre-8.35.tar.gz
mv pcre-8.35 pcre
cd pcre
./config --prefix=自定义用户目录
make 
make install
```
### 3.3 编译zlib
```shell
tar -zxvf  zlib-1.2.11.tar.gz
mv   zlib-1.2.11 zlib
cd zlib
./config --prefix=自定义用户目录
make 
make install
```



&emsp; 
## 4. 编译安装nginx 
注意要写绝对路径
```shell
./configure \
    --prefix=/home/zhangsan/opt/nginx\
    --with-pcre=/home/zhangsan/opt/pcre\
    --with-zlib=/home/zhangsan/opt/zlib\
    --user=zhangsan\
    --with-file-aio\
    --with-http_ssl_module\
    --with-http_realip_module\
    --with-http_sub_module\
    --with-http_gzip_static_module\
    --with-http_stub_status_module

make 
make install
```



&emsp;
## 5. 启动nginx
### 5.1 修改 `nginx.conf`
修改 `/home/zhangsan/opt/nginx/conf/nginx.conf`,将端口从 80 修改到 2000(非root只能使用1024一下端口)：
### 5.2 启动
到安装目录下的 `sbin`下找到二进制文件`nginx`，然后执行下面的命令：
```shell
./nginx -c /home/zhangsan/opt/nginx/conf/nginx.conf
```
### 5.3 验证
```shell
ps -ef | grep nginx      # 看服务起来了没有   
netstat -tln | grep 2000 # 查看端口有没有监听起来
```