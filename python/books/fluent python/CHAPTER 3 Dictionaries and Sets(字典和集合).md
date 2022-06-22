- [1. `Mapping` 和 `MutableMapping`](#1-mapping-和-mutablemapping)
  - [1.1 他俩是什么？](#11-他俩是什么)
  - [1.2 他俩的作用是？](#12-他俩的作用是)
- [2. 可哈希类型(hashable object)](#2-可哈希类型hashable-object)
  - [2.1 什么是可哈希(Hashable)？](#21-什么是可哈希hashable)
  - [2.2 什么样的数据类型是 可哈希的？](#22-什么样的数据类型是-可哈希的)
    - [2.2.1 官方文档](#221-官方文档)
  - [2.3 内建类型哪些是可哈希的？](#23-内建类型哪些是可哈希的)
- [3. `UserDict`](#3-userdict)
  - [3.1 `UserDict`的作用是？](#31-userdict的作用是)
  - [在实现映射类型(mapping type)时，更建议继承`UserDict`，而不是内置`dict`？](#在实现映射类型mapping-type时更建议继承userdict而不是内置dict)
- [4. 不可修改的映射类型](#4-不可修改的映射类型)
  - [4.1 标准库有哪些 不可修改的映射类型？](#41-标准库有哪些-不可修改的映射类型)
  - [4.2 如何使用不可修改的映射类型？](#42-如何使用不可修改的映射类型)






&emsp;
&emsp;
&emsp;
# 1. `Mapping` 和 `MutableMapping` 
## 1.1 他俩是什么？
&emsp;&emsp; `Mapping` 和 `MutableMapping` 这两个抽象基类，它们的作用是为 `dict` 和其
他类似的类型定义形式接口。

## 1.2 他俩的作用是？
&emsp;&emsp; 抽象基类`Mapping` 和 `MutableMapping`的主要作用是作为形式化的文档， 它们定义了构建一个映射类型所需要的最基本的接口。 
&emsp;&emsp; 然后它们还可以跟 `isinstance()` 一起被用来判定某个数据是不是广义上的映射类型 ：
```python
from collections import abc
my_dict = {}
isinstance(my_dict, abc.Mapping)
# True
```






&emsp;
&emsp;
&emsp;
# 2. 可哈希类型(hashable object)
## 2.1 什么是可哈希(Hashable)？
官方文档是这么说的：
> &emsp;&emsp; An object is hashable if it has a hash value which never changes during its lifetime (it needs a `__hash__()` method), and can be compared to other objects (it needs an `__eq__()` method). Hashable objects which compare equal must have the same hash value.
> 
翻一下就是：
> &emsp;&emsp; 如果一个对象是可哈希的， 那么在这个对象的生命周期中， 它的散列值是不变的， 而且这个对象需要实现 `__hash__()`方法。 另外可哈希对象还要有 `__eq__()`方法， 这样才能跟其他键做比较。 如果两个可哈希对象是相等的， 那么它们的哈希值一定是一样的。
> 

## 2.2 什么样的数据类型是 可哈希的？
### 2.2.1 官方文档
官方文档是这么说的：
> &emsp;&emsp; ① Most of Python’s immutable built-in objects are hashable; mutable containers (such as lists or dictionaries) are not; immutable containers (such as tuples and frozensets) are only hashable if their elements are hashable. 
> &emsp;&emsp; ② Objects which are instances of user-defined classes are hashable by default. They all compare unequal (except with themselves), and their hash value is derived from their `id()`.
> 
翻一下就是：
> 
> &emsp;&emsp; ① 在python的内建类型中，大部分不可变类型的都是可哈希的，可变类型(比如`list`和`dict`)都是不可哈希的。不可变类型只有在它包含的元素是可哈希时才是可哈希的(如`tuple`和`frozenset`)。
> &emsp;&emsp; ② 一般情况下，用户自定义的类型的对象都是可散列的，它们的哈希值就是`id()`函数的返回值， 所以所有这些对象在比较的时候都是不相等的。 
> 

## 2.3 内建类型哪些是可哈希的？
&emsp;&emsp; 首先，**可变类型**都是不可哈希的；
&emsp;&emsp; 对于**不可变类型**，`str`、 `bytes` 和`number`都是可哈希的；而元组虽然也是不可变类型，但是元组里面的元素可能是其他可变类型的引用，而只有在元组里面包含的元素都是可哈希类型的时候，元组才是可哈希的。
| 数据类型         | 是否可哈希                                                         |
| ---------------- | ------------------------------------------------------------------ |
| `list`           | 不可哈希，因为是可变类型                                           |
| `dict`           | 不可哈希，因为是可变类型                                           |
| `bytearray`      | 不可哈希，因为是可变类型                                           |
| `set`      | 不可哈希，因为是可变类型                                           |
| `str`            | 可哈希                                                             |
| `bytes`          | 可哈希                                                             |
| `number`         | 可哈希                                                             |
| `tuple`          | 取决于元组内部的元素，若内部元素都可哈希，则可哈希；反之则不可哈希 |
| 用户自定义的类型 | 一般情况下都是可散列的，它们的哈希值就是`id()`函数的返回值         |

来看看实例：
```python
# 可哈希
t1 = (1, 2, (30, 40))
print(hash(t1))

# 不可哈希，因为元组中包含了list
t2 = (1, 2, [30, 40])
print(hash(t2))
```
运行结果：
```
-3907003130834322577
Traceback (most recent call last):
  File "d:\code_practice\practice.py", line 5, in <module>
    print(hash(t2))
TypeError: unhashable type: 'list'
```






&emsp;
&emsp;
&emsp;
# 3. `UserDict`
## 3.1 `UserDict`的作用是？

## 在实现映射类型(mapping type)时，更建议继承`UserDict`，而不是内置`dict`？
&emsp; 建议继承 `UserDict` 而不是从 dict 继承的主要原因是：
> &emsp;&emsp; 内置`dict`的有些方法在实现的时候走一些捷径，如果我们继承内置`dict`，这导致我们不得不要在子类中重写这些方法。但是如果继承`UserDict`，我们就可以直接继承而不需要重写这些方法。
> 






&emsp;
&emsp;
&emsp;
# 4. 不可修改的映射类型
## 4.1 标准库有哪些 不可修改的映射类型？
&emsp;&emsp; 标准库里所有的映射类型都是可变的。
## 4.2 如何使用不可修改的映射类型？
&emsp;&emsp; 从 Python 3.3 开始， `types`模块中引入了一个封装类名叫 `MappingProxyType`。 如果给这个类一个映射，它会返回一个只读的映射视图。 虽然是个只读视图， 但是它是动态的。 这意味着如果对原映射做出了改动， 我们通过这个视图可以观察到， 但是无法通过这个视图对原映射做出修改。 示例如下：
```python
from types import MappingProxyType

# 首先，先把字典建好，里面的内容也写好
d = {
    1 : 'A',
    2 : 'B',
    3 : 'C',
}
# 然后，把建好的字典传给 MappingProxyType，它会返回一个映射视图
d_proxy = MappingProxyType(d)

# 现在可以使用了
print(f'd_proxy : {d_proxy}\n')

print(f'd_proxy[2] : {d_proxy[2]}\n')

# 尝试通过映射视图修改字典
d_proxy[2] = "TTT"
```
运行结果：
```
d_proxy : {1: 'A', 2: 'B', 3: 'C'}

d_proxy[2] : B

Traceback (most recent call last):
  File "d:\code_practice\practice.py", line 14, in <module>
    d_proxy[2] = "TTT"
TypeError: 'mappingproxy' object does not support item assignment
```



