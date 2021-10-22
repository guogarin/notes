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



&emsp;
&emsp;
## 条款2：Follow the PEP 8 Style Guide
### 1. 什么是 PEP 8？




&emsp;
&emsp;
## 条款3：Know the Differences Between bytes and str
### 1. 如何定义`bytes`和`str`？
`bytes`一般是形如`b'Hello'`,`str`前面没有`b`
```python
bytes_tmp = b'h\x65llo'     # bytes
str_tmp = 'a\u0300 propos'  # str
```

### 2. `python`的`bytes`和`str` 分别表示的是什么？
#### 2.1 `bytes`
&emsp;&emsp; `bytes` 包含的是 **原始数据**(8位无符号值，一般用`ASCII`编码)，只负责**以字节序列的形式（二进制形式）来存储数据**，至于这些数据到底表示什么内容（字符串、数字、图片、音频等），完全由程序的解析方式决定。如果采用合适的字符编码方式（字符集），字节串可以恢复成字符串；反之亦然，字符串也可以转换成字节串。
&emsp;&emsp; 说白了，`bytes` 只是简单地记录内存中的原始数据，至于如何使用这些数据，`bytes` 并不在意，你想怎么使用就怎么使用，`bytes` 并不约束你的行为。

#### 2.2 `str`
&emsp;&emsp; 前面已经提到，`bytes`里存的是原始数据，它的存在形式是`01010001110`这种。我们无论是在写代码，还是阅读文章的过程中，肯定不会有人直接阅读这种比特流，它必须有一个编码方式，使得它变成有意义的比特流，而不是一堆晦涩难懂的`01`组合。
&emsp;&emsp; 和`bytes`相对的是，`str` 中包含的是表示人类语言文本字符的`Unicode`数据，这样人们看的时候就方便了。

### 3.  `bytes`和`str`之间如何转换？
> 要将`Unicode`数据转换为二进制数据，必须调用str的`encode`方法。
> 要将二进制数据转换为`Unicode`，必须调用bytes的 `decode` 方法
> 
其实可以这么理解：
> `str`保存的是人能看懂的数据，因此`str`到`bytes`的转换就是 加密(encode)；
> `bytes`保存的是人看不懂的二进制数据，因此`bytes`到`str`的转换就是 解密(decode)。
> 
书中有这样一句话是：
> &emsp;&emsp; Importantly, str instances do not have an associated binary encoding, and bytes instances do not have an associated text encoding.（str实例没有相关联的二进制编码，而bytes实例也没有相关联的文本编码）
> 
这句话的想表达意思是，`str`和`byte`之间没有绑定关系：
> `str`可以加密(encode)为`gbk`编码的二进制序列，也可以加密(encode)为`utf8`编码的二进制序列，加密(encoding)为`gb2312`等其它编码当然也没问题；
> `bytes`可以解码(decode)为`gbk`编码的二进制序列，也可以解码(decode)为`bytes`编码的二进制序列。
> 
我们来验证一下：
```python
>>> a = "python教程"
>>> a.encode("gbk")
# b'python\xbd\xcc\xb3\xcc'
>>> a.encode() # 使用默认编码，即utf8
# b'python\xe6\x95\x99\xe7\xa8\x8b'
>>> b = a.encode()
>>> b.decode()
'python教程'
>>> b
# b'python\xe6\x95\x99\xe7\xa8\x8b'
```
从上面的结果我们可以看到，同样是 `"python教程"`，用`gbk`和`utf8`得到的`bytes`是不一样的，也验证了 `str`和`byte`之间没有绑定关系 这一结论。

### 4. 对于`bytes`数据，为什么 `print()`和`list()`出来的数据不一样呢？
```python
>>> b = b'Hello'
>>> print(b)
# b'Hello'
>>> b
# b'Hello'
>>> print(b.__str__())
# b'Hello'
>>> b[0]
# 72
>>> print(list(b))
# [72, 101, 108, 108, 111]
```
因为`print(obj)`调用的是`obi.__str__()`来输出的，`bytes`类型的`__str__`方法返回的就是这个形式；

### 5. `bytes`和`str`的兼容性
`bytes`和`str`不兼容，它们不能混用：
**(1) 拼接**
通过使用`+`运算符，可以`bytes + bytes` ，`str + str`，但是不能 `bytes + str`
```python
>>> b'hello' + b'world'
# b'helloworld'
>>> 'hello' + 'world'
# 'helloworld'
>>> b'hello' + 'world' 
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't concat str to bytes
```
**(2) 比较**
不能对`bytes`和`str`进行`>`、`<`的比较
```python
>>> assert "hello" > "Hello"

>>> assert "hello" > b"Hello"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '>' not supported between instances of 'str' and 'bytes'
```
对`bytes`和`str`进行`==`，结果总会是返回`False`，即使里面包含了一样的数据。
```python
>>> print(b'foo' == 'foo')
#False
```
**(3) 用`%`格式化字符**
不能用`str`来格式化一个`bytes`字符串；
但可以用`bytes`来格式化一个`str`字符串，因为调用的是`__repr__`来替换`%s`的：
```python
>>> print(b"Hello %s" % b"world")
# b'Hello world'

>>> print("Hello %s" % b"world")
# Hello b'world'

>>> print(b"Hello %s" % "world")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: %b requires a bytes-like object, or an object that implements __bytes__, not 'str'
```

### 6. 读写文件
#### 6.1 写文件需要遵守的方针
**以什么模式打开的，就只能写该类型的字符串：**
> 以文本模式，也就是`Unicode`打开的文件只能写`str`;
> 以二进制打开的文件只能写`bytes`
> 
#### 6.2 写文件 
&emsp;&emsp; 值得注意的是，由`open()`默认以Unicode打开文件。
```python
>>> with open("test.txt", "w") as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 

Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
TypeError: write() argument must be str, not bytes
```
因为`mode('w')`是以 默认模式(文本模式) 打开，而文本模式期望的输入是`str`， 写入的却是二进制数据`b'\xf1\xf2\xf3\xf4\xf5'`， 所以报错。
再来看下面的代码
```python
>>> with open("test.txt", "wb") as f:
...     f.write("World")
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
TypeError: a bytes-like object is required, not 'str'
```
上面的代码用 `mode('wb')`(二进制模式)打开文件，写入的却是`str`类型，所以也报错
正确的做法如下：
```python
>>> with open("test.txt", "wb") as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 
5

>>> with open("test.txt", "w") as f:
...     f.write("World")
... 
5
```
以什么模式打开就写入什么类型的数据，不要混用。

#### 6.3 读文件
类似的问题，在`read()`的时候也存在：
```python
>>> with open('data.bin', 'wb') as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 
5

>>> with open('data.bin', 'r') as f:
...     data = f.read()
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "/usr/local/bin/python3/lib/python3.7/codecs.py", line 322, in decode
    (result, consumed) = self._buffer_decode(data, self.errors, final)
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 0: invalid continuation byte
```
**错误的原因：**
&emsp;&emsp; 因为文件是以 文本模式(`r`)打开的，当文件是以文本模式打开时，它使用系统默认的文件编码(即`utf8`)来翻译二进制文件：
> bytes.encode (for writing) and str.decode (for reading)
> 写文件用`bytes.encode()`，读文件用`str.decode()`
> 
在上面的代码中，前面写入了`b'\xf1\xf2\xf3\xf4\xf5'`，因此后面读的时候就相当于 `str.decode(b'\xf1\xf2\xf3\xf4\xf5')`，因此报错为
```
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 0: invalid continuation byte
```
**但是，反过来就不会报错：**
```python
>>> with open('data.bin', 'w') as f:
...     f.write("Hello World")
... 
#11

>>> with open('data.bin', 'rb') as f:
...     data = f.read()
... 
>>> data
#b'Hello World'
```
**为什么反过来不会报错？**
&emsp;&emsp; 因为文件都是以二进制形式保存的，以 二进制(`rb`)的方式打开当然不会有问题。

### 7. 总结
&emsp;&emsp; ① py3中，有两中类型可以表示字符串：`bytes`和`str`，`bytes`中保存的是8位无符号数，`str`中保存的是`unicode`编码的文本类型；
&emsp;&emsp; ② 不能将`bytes`和`str`用操作符(如`>, ==, +,  %)`进行混合操作；
&emsp;&emsp; ③ 如果想往文件里 读/写 二进制数据，则应该使用 二进制模式打开文件(`rb` 或 `wb`)
&emsp;&emsp; ④ 如果您想在文件中读取或写入Unicode数据，请注意系统的默认文本编码.如果希望避免意外，则显式地指定编码方式传递给`open()`，如 `open('tmp.txt', 'gbk'`、`open('tmp.txt', 'utf8'`)，不能依赖系统默认的编码方式。




&emsp;
&emsp;
## 条款4：Prefer Interpolated F-Strings Over C-style Format Strings and str.format
### 1. Python有哪些格式化字符串的方法？
一共有四种：
① 使用 `%` 来格式化字符串；
② 
③ 
④ 

### 使用 `%` 来格式化字符串
它的缺点：
(1) 一旦顺序或类型错了，会在运行时报错：
```python
value = 1.234
formatted_str = '%-10s = %.2f' % (key, value)
print(formatted_str)
>>> 
# my_var     = 1.23

formatted_str = '%-10s = %.2f' % (value, key)
>>> 
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: must be real number, not str
```
为了避免上述错误，我们必须保证类型和顺序的正确性。

(2) 




① 
② 
③ 
④ 
```python

```
# 参考文献
1. [Python3中的bytes和str类型](https://zhuanlan.zhihu.com/p/102681286)