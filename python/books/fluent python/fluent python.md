- [CHAPTER 10 Sequence Hacking, Hashing, and Slicing (序列的修改、 散列和切片)](#chapter-10-sequence-hacking-hashing-and-slicing-序列的修改-散列和切片)
  - [1. `reprlib`](#1-reprlib)
    - [1.1 作用](#11-作用)
    - [1.2 使用](#12-使用)
  - [2. 协议和鸭子类型(Duck Typing)](#2-协议和鸭子类型duck-typing)






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