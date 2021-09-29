# 一、Pythonic Thinking
## 条款1：Know Which Version of Python You’re Using
### 1. 为什么要确定python的版本？
因为在命令行中，`python`可能意味着`python2`（比如centOS7中）：
```shell
$ python --version
```
输出为
```
Python 2.7.5
```

### 2. 有哪些方法可以获取python的版本？
**(1) 命令行获取**
```shell
$ python3 --version
```
输出为
```
Python 3.7.0a1
```
**(2) 在程序运行时获取**
```python
import sys
print(sys.version_info)
```
运行结果:
```
sys.version_info(major=3, minor=7, micro=0, releaselevel='alpha', serial=1)
3.7.0a1 (default, Jan 12 2019, 22:18:25) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-16)]
```


## 条款2：Follow the PEP 8 Style Guide
### 1. 什么是 PEP 8？



## 条款3：Know the Differences Between bytes and str
