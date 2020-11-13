## 1. 概述
我们常用的文件查找命令有下面几个：
```shell
which       # 查看可执行文件的位置。
whereis     # 查看文件的位置。
locate      # 配合数据库查看文件位置。
find        # 实际搜寻硬盘查询文件名称。
```



## 2. 详解
### 2.1 which
#### 2.2.1  原理
&emsp;`which` 从环境变量PATH中，定位和返回 与指定名字相匹配的 可执行文件 所在的路径。
情况一：找到了：
```shell
which cd    # 查找 cd命令在哪
```
结果：
> /usr/bin/cd

情况二：没找到
```shell
which wwww    # 查找 wwww命令在哪
```
显然系统没有`www`这个命令，因此结果为：
> /usr/bin/which: no www in (/home/zhangsan/opt/mysql/bin/:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/zhangsan/.local/bin:/home/zhangsan/bin)

#### 2.1.2 适用场合
&emsp;&emsp;一般用于查找命令/可执行文件所在的路径。有时候可能在多个路径下存在相同的命令，该命令可用于查找当前所执行的命令到底是哪一个位置处的命令。

# 2.2 whereis

## 3. 总结
<div align="center"> <img src="./pic/linux查找命令比较.png"> </div>
<center> <font color=black> <b> 图3 linux查找命令比较 </b> </font> </center>


## 参考文献
1. [Linux下4个查找命令which、whereis、locate、find的总结](https://blog.csdn.net/u010625000/article/details/44455023)