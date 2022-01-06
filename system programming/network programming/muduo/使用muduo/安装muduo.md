# 1. 安装相关库和工具
## 1.1 安装`cmake`
```bash
yum install cmake
```

## 1.2 安装`Boost`库
```bash
yum install boost
yum install boost-devel
yum install boost-doc
```

## 1.3 几个可选的包
&emsp;&emsp; muduo有三个非必需的依赖库： curl、 c-ares DNS、 Google Protobuf， 如果安装了这三个库， cmake会自动多编译一些示例。 安装方法如下：
```bash
yum -y install curl
yum -y install openssl
yum -y install protobuf
```

# 2. 编译、安装muduo
## 2.1 下载muduo源码
```bash
git clone https://github.com/chenshuo/muduo.git
```
如果没有clone成功，自己去git下载也可以。

## 2.2 编译muduo
进入muduo根目录
```bash
cd muduo # 在即下载的话文件夹名称就不是这个了

# 编译muduo库和它自带的例子
./build.sh -j2
```


```bash

```

# reference
1. [Linux(muduo网络库):18](https://www.codenong.com/cs105104845/)
2. [centos下muduo库的安装与使用](https://blog.csdn.net/qq_34673519/article/details/97753784)