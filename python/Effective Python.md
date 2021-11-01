# 一、Pythonic Thinking
## Item 1：Know Which Version of Python You’re Using
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
&emsp;
## Item 2：Follow the PEP 8 Style Guide
### 1. 什么是 PEP 8？






&emsp;
&emsp;
&emsp;
## Item 3：Know the Differences Between bytes and str
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
&emsp;
## Item 4：Prefer Interpolated F-Strings Over C-style Format Strings and str.format
### 1. Python有哪些格式化字符串的方法？
一共有四种：
> ① 使用 `%` 来格式化 C风格字符串
> ② 使用`dic`来格式化 `%` 来格式化 C风格字符串
> ③ 使用内置的`str`类的`format()`方法
> ④ 使用插值(interpolated format string，简称f-string)

####  1.1 方法一：使用 `%` 来格式化 C风格字符串
##### 1.1.1 优点
简单

##### 1.1.2 缺点：
**问题1：一旦顺序或类型错了，会在运行时报错：**
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

**问题2：当要对 准备填进去的值 做处理时，表达式会变得很冗长**
```python
pantry = [
	('avocados', 1.25),
	('bananas', 2.5),
	('cherries', 15),
]
for i, (item, count) in enumerate(pantry):
	print('#%d: %-10s = %.2f' % (i, item, count))
```
运行结果为：
```
#0: avocados   = 1.25
#1: bananas    = 2.50
#2: cherries   = 15.00
```
如果想让输出的数据更好理解，就需要做一点小小的改动：
```python
for i, (item, count) in enumerate(pantry):
	print('#%d: %-10s = %d' % (
		i + 1,
		item.title(), 
		round(count)))
```
运行结果为：
```
#1: Avocados   = 1
#2: Bananas    = 2
#3: Cherries   = 15
```
可以看到的是，代码在修改过之后变得很冗长，可读性变差了。

**问题3：如果想用同一个值来填充格式字符里的多个位置，那就必须在`%`后面的元组中多次重复该值**
```python
template = '%s loves food. See %s cook.'
name = 'Max'
formatted = template % (name, name) # name变量需要写两次
print(formatted)
```
可以看到的是，`name`变量需要写两次，而且如果需要修改`name`，则需要改两次，这很麻烦，而且还很容易漏。

**问题4：**

### 1.2 方法二：使用`dic`来格式化 `%` 来格式化 C风格字符串
#### 1.2.1 优点
用`dic`来替代元组，这可以解决**问题1**和**问题3**：
解决问题1：
```python
key = 'my_var'
value = 1.23414  

old_way = '%-10s = %.2f' % (key, value)

new_way = '%(key)-10s = %(value).2f' % {
	'key': key, 'value': value} # Original
reordered = '%(key)-10s = %(value).2f' % {
	'value': value, 'key': key} # 调换了位置也不影响
assert old_way == new_way == reordered
```
解决问题3：
```python
name = 'Max'

template = '%s loves food. See %s cook.'
before = template % (name, name) # %右侧为元组

template = '%(name)s loves food. See %(name)s cook.'
after = template % {'name': name} # %右侧为字典

assert before == after
```

#### 1.2.2 缺点
用`dic`来替代元组 让 **问题2** 变得更严重了：
```python
for i, (item, count) in enumerate(pantry):
	before = '#%d: %-10s = %d' % (
		i + 1,
		item.title(),
		round(count))

	after = '#%(loop)d: %(item)-10s = %(count)d' % {
		'loop': i + 1,
		'item': item.title(),
		'count': round(count),
		}

	assert before == after	
```
我们可以看到的是，用`dic`来替代元组后，代码变得更为冗长了，可读性也随之下降。

### 1.3 方法三：使用内置的`str`类的`format()`方法
`str.formant()`

### 1.4 方法四：使用 插值格式字符串(interpolated format string，简称f-string)
&emsp;&emsp; 插值格式字符串 是在`Python3.6`中引入的特性，可以完美解决前面提到的问题。
#### 1.4.1 怎么使用 插值格式字符串？
① 在格式字符的前面加上`f`，比如`f'{key} : {value}'`；
② 可以直接在`f-string`的`{}`里面直接引用当前作用域可见的变量；
③ 还能直接进行函数调用：`{round(count)}`；
④ 支持`str.format`那套迷你语言(也就是在`{}`内的冒号右侧采用的那套规则)：`{item.title():^20s}`
```python
panpantry = [
    ('avocados', 1.25),
    ('bananas', 2.5),
    ('cherries', 15),
]

for i, (item, count) in enumerate(pantry):
    print(f"#{i+1}: {item.title():^20s} = {round(count)}") 
```

#### 1.4.2 `str.format()`方法 和 `f-string`的联系
&emsp;&emsp; `f-string`支持`str.fformat()`所支持的 那套迷你语言，也就是在`{}`内的冒号右侧采用的那套规则也可以用到`f-sting`里面，而且也可以通过`!`把值转换成`Unicode`及`repr`形式的字符串。

#### 1.4.3 插值格式字符串 好在哪？
&emsp;&emsp; **可以直接在 格式字符串内 直接引用 变量**，这个特性彻底解决了前面四个问题，既简洁明了，又不存在顺序弄错的问题，下，下面的代码对四种方法进行了对比，结论一目了然：
```python
pantry = [
    ('avocados', 1.25),
    ('bananas', 2.5),
    ('cherries', 15),
]

for i, (item, count) in enumerate(pantry):
	c_style_tuple = '#%d: %-10s = %d' % (
		i + 1,
		item.title(),
		round(count))
	print(c_style_tuple)

	c_style_dic = '#%(loop)d: %(item)-10s = %(count)d' % {
		'loop': i + 1,
		'item': item.title(),
		'count': round(count),
	}
	print(c_style_dic)

	str_format = '#{}: {:<10s} = {}'.format(
		i + 1,
		item.title(),
		round(count))
	print(str_format)

	# f-string 一行代码搞定
    print(f"#{i+1}: {item.title():^20s} = {round(count)}") 
```






&emsp;
&emsp;
&emsp;
## Item 5：Write Helper Functions Instead of Complex Expressions(用辅助函数取代复杂的表达式)
### 1. 这条规则指的是？
&emsp;&emsp; 对于那些复杂的表达式，尤其是会重复利用的那种复杂表达式，应该定义一个辅助函数来完成。

### 2. 为什么？
① 可读性强，更容易被他人理解；
② 遵循`DRY`原则(Do't Repeat Yourself)，能复用的代码都应该封装成一个函数，可避免代码冗长。






&emsp;
&emsp; 
&emsp; 
## Item 6: Prefer Multiple Assignment Unpacking Over Indexing(把数据直接解包到多个变量里，不要通过下标范围)
### 1. 为什么不建议使用下标访问？
使用下标访问会降低程序的可读性，还能减少代码量：
```python
snacks = [('bacon', 350), ('donut', 240), ('muffin', 190)]

# 用下标访问
for i in range(len(snacks)):
    item = snacks[i]
    name = item[0]
    calories = item[1]
    print(f'#{i+1}: {name} has {calories} calories')

print("*"*30)

# 解包到多个变量中
for rank, (name, calories) in enumerate(snacks, 1):
    print(f'#{rank} : {name.title()} has {calories} calories.')
```
运行结果：
```
#1: bacon has 350 calories
#2: donut has 240 calories
#3: muffin has 190 calories
******************************
#1 : Bacon has 350 calories.
#2 : Donut has 240 calories.
#3 : Muffin has 190 calories.
```
显然，用`unpacking`和`enumerate`函数代码量减少了很多，可行性也大大的提高了。





&emsp;
&emsp;
&emsp;
## Item 7:  Prefer enumerate Over range(尽量使用 enumerate 取代 range)
### 1. 相比于`range()`，`enumerate()`的优势是？
有时在迭代`list`时，需要获取当前处理的元素在`list`中的位置，`enumerate()`会简洁一些：
```python
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']

for i in range(len(flavor_list)):
	flavor = flavor_list[i]
	print(f'{i + 1}: {flavor}')

# 使用 enumerate()
for i, flavor in enumerate(flavor_list, 1):
	print(f'{i}: {flavor}')
```
这又回到了`item 6`，避免使用下标访问容器。





&emsp;
&emsp;
&emsp;
## Item 8: Use zip to Process Iterators in Parallel(用 zip()函数 同时遍历两个迭代器)
### 8.1 用zip() 迭代两个迭代器的优势是？
更为简洁，换句话说就是更`pythonic`，来看一段代码对比：
```python
names = ['Cecilia', 'Lise', 'Marie']
counts = [len(n) for n in names]

longest_name = None
max_count = 0

# 用range()
for i in range(len(names)):
	count = counts[i]
	if count > max_count:
		longest_name = names[i]
		max_count = count

# 用 enumerate()
for i, name in enumerate(names):
	count = counts[i]
	if count > max_count:
		longest_name = name
		max_count = count		

# 用 zip() + unpacking机制
# zip(names, counts)负责将元素封装成元组，然后在用 unpacking机制将元组里的值赋给  name和count
for name, count in zip(names, counts):
	if count > max_count:
		longest_name = name
		max_count = count		
```






&emsp;
&emsp;
&emsp;
## Item 9: Avoid else Blocks After for and while Loops(不要在while和for循环后面写else块)
### 1. 为什么要避免使用？
&emsp;&emsp; 因为 `for … else`、`while … else`，`else` 和 `if/else`的语法不一样，这很容易让人产生误解。






&emsp;
&emsp;
&emsp;
## Item 10: Prevent Repetition with Assignment Expressions(用赋值表达式减少重复代码)
### 1. 赋值表达式好在哪？
① 节省代码
② 可读性好

### 2. 怎么用？
见其它位置的笔记






&emsp;
&emsp;
&emsp;
# 二、Lists and Dictionaries 
## Item 11: Know How to Slice Sequences(学会对序列切片)
### 1. 什么样的类可以进行切片操作？
&emsp;&emsp; 实现了 `__getitem__ `and `__setitem__` 的类都可以进行切片操作。

### 2. 切片时应该秉承什么样的原则？
&emsp;&emsp; 切片要尽可能写的简答，如果是从头开始切割，则应该省略左侧的下标`0`；如果是一直取到末尾，那就应该省略冒号右侧的下标
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

from_start = numbers[:4]
print(from_start)

print(to_end:=numbers[1:])
```
输出的结果：
```
[0, 1, 2, 3]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### 3. `a`和`b`都是列表，`a = b` 和 `a = b[:]` 有何区别？
&emsp;&emsp; 切片`a = b[:]` 是 浅拷贝， `a = b`是直接赋值，所以它们之间的区别就是 直接赋值 和 浅拷贝的关系。来写段代码验证一下：
```python
a = [0, 1, 2, ['a', 'b', 'c'], 4]
b = a[:]

if a == b :
    print("a == b")

a[0] = 'A' 		# 修改列表
a[3][0] = 100	# 修改子序列

print("a = ", a)
print("b = ", b)
```
运行结果：
```
a == b
a =  ['A', 1, 2, [100, 'b', 'c'], 4]
b =  [0, 1, 2, [100, 'b', 'c'], 4]
```
**结果分析：**
&emsp;&emsp; 运行结果显示，列表`a`的直接修改没有影响到列表`b`，但是对列表`a`的子序列的修改却影响到了`b`，这显然验证了 切片 是浅拷贝的结论。






&emsp;
&emsp;
&emsp;
## Item 12: Avoid Striding and Slicing in a Single Expression(不要在切片操作里同时指定 起止下标 和 步长)
### 原因
&emsp;&emsp; 可读性不好，其他人很难理解






&emsp;
&emsp;
&emsp;
## Item 13: Prefer Catch-All Unpacking Over Slicing(尽量通过带星号的`unpacking`操作来捕获多个元素，而不是通过切片)
### 1. 如何通过`unpacking`操作来完成 切片 的功能？
&emsp;&emsp; 搭配 带星号表达式(starred expressiong) 即可：
```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_descending = sorted(car_ages, reverse=True)
oldest, second_oldest , *others = car_ages_descending
```

### 2. 相比于切片，`unpacking`操作捕获的优势在哪？
> ① 简短易读；
> ② 用切片对序列进行切分的时候更容易出错
> 
就拿取序列开头两个元素来举例吧：
```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_descending = sorted(car_ages, reverse=True)

# 解包
oldest, second_oldest , *others = car_ages_descending

print(oldest, second_oldest, others)

# 切片
oldest = car_ages_descending[0]
second_oldest = car_ages_descending[1]
others = car_ages_descending[2:]

print(oldest, second_oldest, others)
```
从上面的代码可知，解包操作不但代码少，而且读起来也更通畅；而且切片操作还得注意下标，很容易出错。






&emsp;
&emsp;
&emsp;
## Item 14: Sort by Complex Criteria Using the key Parameter(用 `sort`方法的`key`参数 来表示复杂的排序逻辑)
### 1. 为什么要用`key`参数指定排序逻辑？
对于一些自定义的类，无法用`sort()`进行排序，此时可通过`key`参数来指定排序规则。
```python
class Tool:
	def __init__(self, name, weight):
		self.name = name
		self.weight = weight
	def __repr__(self):
		return f'Tool({self.name!r}, {self.weight})'

tools = [
	Tool('level', 3.5),
	Tool('hammer', 1.25),
	Tool('screwdriver', 0.5),
	Tool('chisel', 0.25),
]

tools.sort()
```
运行结果：
```
Traceback (most recent call last):
  File "test.py", line 15, in <module>
    tools.sort()
TypeError: '<' not supported between instances of 'Tool' and 'Tool'
```
将上面的代码修改如下：
```python
tools.sort(key=lambda x : x.name)
print(tools)
```
运行结果如下：
```
[Tool('chisel', 0.25), Tool('hammer', 1.25), Tool('level', 3.5), Tool('screwdriver', 0.5)]
```

### 2. 如何用多个条件来排序？
#### 2.1 利用 元组 来实现
&emsp;&emsp; 我们都知道，两个元组在比较的时候，如果首元素相等，那就比较第二个元素，如果第二个也相等，那就继续往下比较，利用元组的这个特性，我们可以实现多条件排序。
&emsp;&emsp; 比如我们希望`tools`先以`weight`来排序，在`weight`相等的情况下再以`name`排序。我们可以构造一个元组`(weight, name)`：
```python
tools.sort(key=lambda x : (x.weight, x.name))
print(tools)
```
运行结果：
```
[Tool('chisel', 0.25), Tool('screwdriver', 0.5), Tool('hammer', 1.25), Tool('level', 3.5)]
```
**但是利用元组来实现有一个不足之处：** 如果多个指标中，我们希望一个指标排正序，一个排倒序，元组就不能实现了。

#### 2.2 多次调用`sort()`
&emsp;&emsp; 因为`sort()`是稳定排序(准确的说是`timsort`，一种改进过的归并排序)，这意味着如果`key`函数认为两个值相等，那么这两个值在排序结果中的先后顺序会与它们在排序前的顺序一致，因此多次调用`sort()`不会有问题。

#### 2.3 如果多个条件中，我们希望一个条件排正序，一个排倒序，应该怎么做？
**① 用元组**
&emsp;&emsp; 如果这多个条件中有一个是数字，可以通过对为数字的条件取倒数来完成
**② 多次调用`sort()`，对需要逆序的那个用`reverse=True`参数来指定逆序。**






&emsp;
&emsp;
&emsp;
## Item 15: Be Cautious When Relying on dict Insertion Ordering(不要过分依赖给字典添加条目时所用的顺序)
### 1. 字典的`key`是否有序？
&emsp; 在`Python3.7`开始，我们可以确信迭代 标准的字典(注意是标准的字典) 时
> &emsp;&emsp; 在`Python 3.5`之前 的版本中，`dict`所提供的许多方法（包括`keys`、`values`、i`tems`与`popitem`等）都不保证固定的顺序。
> &emsp;&emsp; 从`Python 3.6`开始，字典会保留这些 键值对 **在添加时所用的顺序**，而且`Python3.7`版的语言规范正式确立了这条规则。
> 

### 2. 为什么不能总是假设所有的字典都能保留键值对插入时的顺序？
&emsp;&emsp; 因为在`python`中，我们很容易就拿自定义出 和标准`dict`很像，但本身不是字典的类，而对于这些类型的对象，我们不能假设 迭代时看到的顺序 会和 插入的顺序一致。
#### 2.2 如何解决这个问题呢？
① 重新定义函数，让这个函数不依赖插入时的顺序；

② 在函数的开头先判断传进来的对象是不是 标准的`dict`对象：
```python
def get_winner(ranks):
	if not isinstance(ranks, dict):
		raise TypeError('must provide a dict instance')
	return next(iter(ranks))
```

③ 通过 注解(type annotation) 来保证传过来的是 标准的`dict`对象
&emsp;&emsp; 






&emsp;
&emsp;
&emsp;
## Item 16: Prefer get Over in and KeyError to Handle Missing Dictionary Keys(用`get`处理键不在字典里的情况，而不是`in`和`KeyError`)
### 1. 有哪些方法 可以处理 key不在字典里的情况？

### 2. 更推荐哪种方法？为什么？








① 
② 
③ 
④ 
```python

```
# 参考文献
1. [Python3中的bytes和str类型](https://zhuanlan.zhihu.com/p/102681286)