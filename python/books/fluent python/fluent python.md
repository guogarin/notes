- [CHAPTER 10 Sequence Hacking, Hashing, and Slicing (序列的修改、 散列和切片)](#chapter-10-sequence-hacking-hashing-and-slicing-序列的修改-散列和切片)
  - [1. `reprlib`](#1-reprlib)
    - [1.1 作用](#11-作用)
    - [1.2 使用](#12-使用)
  - [2. 协议和鸭子类型(Duck Typing)](#2-协议和鸭子类型duck-typing)
  - [Vector类第3版： 动态存取属性](#vector类第3版-动态存取属性)
  - [Vector类第4版： 散列和快速等值测试](#vector类第4版-散列和快速等值测试)
- [CHAPTER 11 Interfaces: From Protocols to ABCs(接口： 从协议到抽象基类)](#chapter-11-interfaces-from-protocols-to-abcs接口-从协议到抽象基类)
- [CHAPTER 12 Inheritance: For Good or For Worse(继承的优缺点)](#chapter-12-inheritance-for-good-or-for-worse继承的优缺点)
- [CHAPTER 13 Operator Overloading: Doing It Right(正确重载运算符)](#chapter-13-operator-overloading-doing-it-right正确重载运算符)






&emsp;
&emsp;
&emsp;
# CHAPTER 10 Sequence Hacking, Hashing, and Slicing (序列的修改、 散列和切片)
## 1. `reprlib`
### 1.1 作用
&emsp;  **It's provided a limited-length representation：**
> &emsp;&emsp; When a container has more than six components, the string produced by `repr()` is abbreviated with `...`. This is crucial in any collection type that may contain a large number of items, because repr is used for debugging(and you don’t want a single large object to span thousands of lines in your console or log). Use the reprlib module to produce limited-length representations.
> 

### 1.2 使用
```python
import reprlib
from array import array

class Vector:
    typecode = 'd'
    def __init__(self, components):
        self._components = array(self.typecode, components)

    def __repr__(self):
        components = reprlib.repr(self._components)
        components = components[components.find('['):-1]
        return 'Vector({})'.format(components)


vec = Vector(range(100)) # a vector with 100 dimensions
print(vec) 
```
运行结果：
```
Vector([0.0, 1.0, 2.0, 3.0, 4.0, ...])
```
可以看到的是，第六个元素开始，都缩写成了`...`，这在打印日志的时候很有用。



&emsp;
&emsp;
## 2. 协议和鸭子类型(Duck Typing)



&emsp;
&emsp;
## Vector类第3版： 动态存取属性
&emsp;&emsp; `Vector2d`（二维）变成 `Vector`（n维）之后，就没办法通过名称访问向量的分量了（如 `v.x` 和 `v.y`）。现在我们处理的向量可能有大量分量。不过，若能通过单个字母访问前几个分量的话会比较方便。比如，用 `x`、`y` 和 `z` 代替`v[0]`、`v[1]`和`v[2]`。
&emsp;&emsp; 在 `Vector2d` 中， 我们使用 `@property` 装饰器把 `x` 和 `y` 标记为只读特性（见示例 9-7） 。 我们可以在Vector 中编写四个特性， 但这样太麻烦。 特殊方法 `__getattr__` 提供了更好的方式：
```python
shortcut_names = 'xyzt'

def __getattr__(self, name):
    # # 获取 Vector， 后面待用
    cls = type(self) 
    if len(name) == 1: # 如果属性名只有一个字母， 可能是 shortcut_names 中的一个
        # 查找那个字母的位置； 
        pos = cls.shortcut_names.find(name) 
    # 如果位置落在范围内， 返回数组中对应的元素。
    if 0 <= pos < len(self._components): 
        return self._components[pos]
    # 如果测试都失败了， 抛出 AttributeError， 并指明标准的消息文本。
    msg = '{.__name__!r} object has no attribute {!r}' 
    raise AttributeError(msg.format(cls, name))
```



&emsp;
&emsp;
## Vector类第4版： 散列和快速等值测试
`Vector2d`（二维）中的 `__hash__` 方法简单地计算 `hash(self.x) ^ hash(self.y)`：
```python
class Vector2d:

    # 略... 

    def __hash__(self):
        return hash(self.x) ^ hash(self.y)
```
`Vector`（n维）就要使用`^`（异或）运算符依次计算各个分量的散列值： `v[0] ^ v[1] ^ v[2]...`。此时`functools.reduce()`函数将派上用场，计算聚合异或的 3 种方式： 一种使用 `for` 循环， 两种使用 `reduce` 函数:
```python
from functools import reduce
from operator import xor

reduce(lambda a,b: a*b, range(1, 6))

n = 0
for i in range(1, 6): # ➊
    n ^= i

reduce(lambda a, b: a^b, range(6)) # ➋

reduce(xor, range(6)) # ➌
```
新的`Vector.__hash__` 方法如下：
```python
from array import array
import reprlib
import math
import functools 
import operator 

class Vector:
    typecode = 'd'

    # 排版需要， 省略了很多行...

    def __eq__(self, other): # ➌
        return tuple(self) == tuple(other)

    def __hash__(self):
        # 创建一个生成器表达式， 惰性计算各个分量的散列值。
        hashes = (hash(x) for x in self._components) 
        return functools.reduce(operator.xor, hashes, 0) 

    # 省略了很多行...
```
如果把上面的代码中的生成器表达式替换成`map` 方法， 映射过程更明显：
```python
def __hash__(self):
    hashes = map(hash, self._components)
    return functools.reduce(operator.xor, hashes)
```
上面的代码在py3中效率不比前面的生成器表达式的版本性能差，但是在py2中就性能低下了，因为：
> 在 Python 2 中使用 `map` 函数效率低些， 因为 `map` 函数要使用结果构建一个列表。 但是在Python 3 中， `map` 函数是惰性的， 它会创建一个生成器， 按需产出结果， 因此能节省内存——这与示例 10-12 中使用生成器表达式定义 `__hash__` 方法的原理一样。
> 
另外，上面的`__eq__()`也实现的太过于简单，我们可以将其修改成：
```python
def __eq__(self, other):
    if len(self) != len(other): # ➊
        return False
    for a, b in zip(self, other): # ➋
        if a != b: # ➌
            return False
    return True # ➍
```
如果用`all()`，还可以将`__eq__()`改成：
```python
def __eq__(self, other):
    return len(self) == len(other) and all(a == b for a, b in zip(self, other))
```






&emsp;
&emsp;
&emsp;
# CHAPTER 11 Interfaces: From Protocols to ABCs(接口： 从协议到抽象基类)






&emsp;
&emsp;
&emsp;
# CHAPTER 12 Inheritance: For Good or For Worse(继承的优缺点)






&emsp;
&emsp;
&emsp;
# CHAPTER 13 Operator Overloading: Doing It Right(正确重载运算符)

