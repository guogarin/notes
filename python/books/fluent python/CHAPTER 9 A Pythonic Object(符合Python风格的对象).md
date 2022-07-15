- [1. 对象表示形式(Object Representations)](#1-对象表示形式object-representations)
  - [1.2 python中获取对象的字符串表示形式的标准方式有哪些？](#12-python中获取对象的字符串表示形式的标准方式有哪些)






&emsp;
&emsp;
&emsp;
# 1. 对象表示形式(Object Representations)
## 1.2 python中获取对象的字符串表示形式的标准方式有哪些？
获取对象的字符串表示形式的标准方式，Python提供了两种：
(1) `repr()`
> 以**便于开发者理解的方式**返回对象的字符串表示形式。用`__repr__`实现
> 
(2) `str()`
> 以**便于用户理解的方式**返回对象的字符串表示形式。用`__str__`实现
> 
其他的表示形式，还有：
(3) `__bytes__` ：`bytes()`函数调用它获取对象的字节序列表示形式。
(4) `__format__` ：会被内置的`format()`函数和`str.format()`方法调用，适应特殊的格式代码显示对象字符串表示形式。
另外：在Python3中，`__repr__`/`__str__`和`__format__`都必须返回`Unicode`字符串（`str`类型）。只有`__bytes__`方法返回字节序列（`bytes`类型）

