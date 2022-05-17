- [1. print](#1-print)
    - [1.1 区别](#11-区别)
    - [1.2 为什么要作出这样的变化？](#12-为什么要作出这样的变化)
- [2. 编码](#2-编码)
    - [2.1 区别](#21-区别)
    - [3.2 为什么要作出这样的变化？](#32-为什么要作出这样的变化)
- [3. 字符串](#3-字符串)
    - [3.1 区别](#31-区别)
    - [3.2 为什么要作出这样的变化？](#32-为什么要作出这样的变化-1)
- [4. True和False](#4-true和false)
    - [4.1 区别](#41-区别)
    - [4.2 为什么要作出这样的变化？](#42-为什么要作出这样的变化)
- [5. nonlocal](#5-nonlocal)
    - [5.1 区别](#51-区别)
    - [5.2 为什么要作出这样的变化？](#52-为什么要作出这样的变化)
- [6. 数据类型](#6-数据类型)
    - [6.1 区别](#61-区别)
- [7. 不等运算符](#7-不等运算符)
- [8. 除法运算](#8-除法运算)
- [9. 一些函数的返回值的修改](#9-一些函数的返回值的修改)
- [10. 错误处理](#10-错误处理)
- [11. 八进制字面量表示](#11-八进制字面量表示)
- [12. 去掉了repr表达式``](#12-去掉了repr表达式)



&emsp;
## 1. print
#### 1.1 区别 
| 版本    | 区别                 |
| ------- | -------------------- |
| python2 | print是一个 **语句** |
| python3 | print是一个 **函数** |

#### 1.2 为什么要作出这样的变化？
> &emsp; (1) print不是函数，不能使用help()，对使用者不方便
> &emsp; (2) print()函数相比print语句多了两个新功能，自定义间隔符（默认空格）和结束符（默认回车）
> &emsp; (3) print()函数重定向输出文件更加方便。
> 



&emsp;
## 2. 编码
#### 2.1 区别
| 版本    | 区别                  |
| ------- | --------------------- |
| python2 | 默认编码是 **asscii** |
| python3 | 默认编码是 **UTF-8**  |
#### 3.2 为什么要作出这样的变化？
&emsp;&emsp;由于pyhton诞生的时候Unicode还没出现,因此python2使用的默认编码格式是ascii编码，这也是python2经常出现编码问题的原因之一，毕竟asscii只收录了少数字符。
&emsp;&emsp;可以通过改变解释器的默认编码格式，但这并不能解决所有的编码问题。



&emsp;
## 3. 字符串
#### 3.1 区别
| py2     | py3  | 表现 | 转换   | 作用       |
| ------- | ---- | ---- | ------ | ---------- |
| str     | byte | 字节 | encode | 存储、传输 |  
| unicode | str  | 字符 | decode | 展示       |
#### 3.2 为什么要作出这样的变化？
&emsp;&emsp;在 Python2 中，字符串有两个类型，一个是 unicode，一个是 str，前者表示文本字符串，后者表示字节序列，不过两者并没有明显的界限，开发者也感觉很混乱，不明白编码错误的原因，不过在 Python3 中两者做了严格区分，分别用 str 表示字符串，byte 表示字节序列，任何需要写入文本或者网络传输的数据都只接收字节序列，这就从源头上阻止了编码错误的问题。



&emsp;
## 4. True和False
#### 4.1 区别
&emsp;&emsp;True 和 False 在 Python2 中是两个全局变量（名字），在数值上分别对应 1 和 0
#### 4.2 为什么要作出这样的变化？
```python
>>> True = False
>>> True
False
>>> True is False
True
>>> False = "x"
>>> False
'x'
>>> if False:
...     print("?")
... 
?
```
&emsp;&emsp;显然，上面的代码违背了 Python 的设计哲学 Explicit is better than implicit.。而 Python3 修正了这个缺陷，True 和 False 变为两个关键字，永远指向两个固定的对象，不允许再被重新赋值。
```python
>>> True = 1
  File "<stdin>", line 1
SyntaxError: can't assign to keyword
```



&emsp;
## 5. nonlocal
#### 5.1 区别
&emsp;&emsp;python3增加了关键字 nonlocal
#### 5.2 为什么要作出这样的变化？
&emsp;&emsp;在Python2中可以在函数里面可以用关键字 global 声明某个变量为全局变量，但是在嵌套函数中，想要给一个变量声明为非局部变量是没法实现的，在Pyhon3，新增了关键字 nonlcoal，使得非局部变量成为可能。



&emsp;
## 6. 数据类型
#### 6.1 区别
1) python3 去掉了`long`类型，现在只有一种整型——int，但它的行为就像python2中的long
2) 新增了bytes类型，对应于2.X版本的八位串，定义一个bytes字面量的方法如下：
```python
>>> b = b"yesterday"
>>> type(b)
<class 'bytes'>
```


&emsp;
## 7. 不等运算符
&emsp;&emsp; python3 去掉了`<>`,只保留了 `!=` 一种写法



&emsp;
## 8. 除法运算
在 Python2 中 `/` 除法就跟我们熟悉的大多数语言，比如 Java 和 C ，整数相除的结果是一个整数，把小数部分完全忽略掉，浮点数除法会保留小数点的部分得到一个浮点数的结果。

在 Python3 中 `/` 除法不再这么做了，对于整数之间的相除，结果也会是浮点数。
```python
# Python 2.x:
>>> 1 / 2
0
>>> 1.0 / 2.0
0.5

#Python 3.x:
>>> 1/2
0.5
```



&emsp;
## 9. 一些函数的返回值的修改
&emsp;&emsp; 在 Python2 中很多返回列表对象的内置函数和方法 在Python3都改成了返回类似于迭代器的对象。**因为迭代器的惰性加载特性使得操作大数据更有效率。**
&emsp;&emsp; 例如，在Python2中有 `range` 和 `xrange` 两个方法。其区别在于，`range`返回一个`list`，在被调用的时候即返回整个序列；`xrange`返回一个`iterator`，在每次循环中生成序列的下一个数字。`Python3`中不再支持 `xrange` 方法，`Python3`中的 `range` 方法就相当于 `Python2`中的 `xrange` 方法。




&emsp;
## 10. 错误处理
&emsp;&emsp; 在 Python3 中处理异常也轻微的改变了，在 Python3 中我们现在使用 `as` 作为关键词。捕获异常的语法由 `except exc, var` 改为 `except exc as var`：
Python2:
```python
try:
    trying_to_check_error
except NameError, err: # 在Python 3.x中不支持
    print err, 'Error Caused' 
```
Python3:
```python
try:
     trying_to_check_error
except NameError as err: # 'as'在Python 3.x中使用
     print (err, 'Error Caused')
```




&emsp;
## 11. 八进制字面量表示
&emsp;&emsp; 在python2中，八进制数有两种写法：
> `0o1000`
> `01000`
> 
但是在Python3中，表示八进制字面量的方式只有一种，就是`0o1000`
```bash
>>> 0o1000
512
>>> 01000
  File "<stdin>", line 1
    01000
        ^
SyntaxError: invalid token
```




&emsp;
## 12. 去掉了repr表达式``
Python2中`(反引号``)`相当于`repr`函数的作用
Python3中去掉了中`(反引号``)`这种写法，只允许直接使用`repr`函数。
