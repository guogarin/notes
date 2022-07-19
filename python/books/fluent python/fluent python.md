- [CHAPTER 10 Sequence Hacking, Hashing, and Slicing (序列的修改、 散列和切片)](#chapter-10-sequence-hacking-hashing-and-slicing-序列的修改-散列和切片)
  - [1. `reprlib`](#1-reprlib)
    - [1.1 作用](#11-作用)
    - [1.2 使用](#12-使用)
  - [2. 协议和鸭子类型(Duck Typing)](#2-协议和鸭子类型duck-typing)
  - [Vector类第3版： 动态存取属性](#vector类第3版-动态存取属性)
  - [Vector类第4版： 散列和快速等值测试](#vector类第4版-散列和快速等值测试)






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
`Vector`（n维）就要使用`^`（异或）运算符依次计算各个分量的散列值： `v[0] ^ v[1] ^ v[2]...`。此时`functools.reduce()`函数将派上用场