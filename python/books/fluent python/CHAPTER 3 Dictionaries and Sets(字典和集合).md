- [1. `Mapping` 和 `MutableMapping`](#1-mapping-和-mutablemapping)
  - [1.1 他俩是什么？](#11-他俩是什么)
  - [1.2 他俩的作用是？](#12-他俩的作用是)
- [2.](#2)
- [3. `UserDict`](#3-userdict)
  - [3.1 `UserDict`的作用是？](#31-userdict的作用是)
  - [在实现映射类型(mapping type)时，更建议继承`UserDict`，而不是内置`dict`？](#在实现映射类型mapping-type时更建议继承userdict而不是内置dict)
- [4. 不可修改的映射类型](#4-不可修改的映射类型)
  - [4.1 标准库有哪些 不可修改的映射类型？](#41-标准库有哪些-不可修改的映射类型)
  - [4.2 如何使用不可修改的映射类型？](#42-如何使用不可修改的映射类型)
- [5. 集合论(set theory)](#5-集合论set-theory)
  - [5.1 有哪些built-in集合类型？](#51-有哪些built-in集合类型)
  - [5.2 如何计算 合集、交集和差集？](#52-如何计算-合集交集和差集)
  - [5.2 如何创造一个空集合？](#52-如何创造一个空集合)
  - [5.3 `{1, 2, 3}` 和`set([1, 2, 3]` 这两种构造方法 在速度上有差异吗？为什么？](#53-1-2-3-和set1-2-3-这两种构造方法-在速度上有差异吗为什么)
- [6. `dict`和`set`的背后](#6-dict和set的背后)
  - [6.1 Python 里的`dict`和`set`的效率有多高？](#61-python-里的dict和set的效率有多高)
  - [6.2 散列表(Hash Table，也叫哈希表)的数据结构是怎样的？](#62-散列表hash-table也叫哈希表的数据结构是怎样的)
  - [6.为什么`dict`和`set`是无序的？](#6为什么dict和set是无序的)
  - [6.为什么并不是所有的 Python 对象都可以当作 `dict` 的键或 `set` 里的元素？](#6为什么并不是所有的-python-对象都可以当作-dict-的键或-set-里的元素)
  - [6.为什么 `dict` 的键和 `set` 元素的顺序是跟据它们被添加的次序而定的， 以及为什么在映射对象的生命周期中， 这个顺序并不是一成不变的？](#6为什么-dict-的键和-set-元素的顺序是跟据它们被添加的次序而定的-以及为什么在映射对象的生命周期中-这个顺序并不是一成不变的)
  - [6.为什么不应该在迭代循环 `dict` 或是 `set` 的同时往里添加元素？](#6为什么不应该在迭代循环-dict-或是-set-的同时往里添加元素)






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
# 2. 



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






&emsp;
&emsp;
&emsp;
# 5. 集合论(set theory)
## 5.1 有哪些built-in集合类型？
&emsp;&emsp; 有`set`和`frozenset`，`set`可修改，而`frozenset`不可修改：
```python
# 可变集合
s = {'A', 'B', 'C'}

# 不可变集合
fs = frozenset(['1', '2', '3'])

print(fs)
```
运行结果：
```
frozenset({'2', '1', '3'})
```

## 5.2 如何计算 合集、交集和差集？
给定两个集合 `a` 和 b：
> &emsp;&emsp; `a | b` : 合集
> &emsp;&emsp; `a & b` : 交集
> &emsp;&emsp; `a - b` : 差集
> 

## 5.2 如何创造一个空集合？
&emsp;&emsp; 如果要创建一个空集合，你必须用不带任何参数的构造方法 `set()`。 如果只是写成 `{}` 的形式， 跟以前一样， 你创建的其实是个空字典。
```python
# 空字典
s1 = {}

# k
s2 = set()
```

## 5.3 `{1, 2, 3}` 和`set([1, 2, 3]` 这两种构造方法 在速度上有差异吗？为什么？
&emsp; `{1, 2, 3}`的速度更快，因为 `set([1, 2, 3]` 
> &emsp;&emsp; 对于`set([1, 2, 3]`，Python 必须先从 `set`这个名字来查询构造方法，然后新建一个列表`[1, 2, 3]`， 最后再把这个列表传入到构造方法里。 
> &emsp;&emsp; 但是对于 `{1, 2, 3}` 这样的字面量， Python会利用一个专门的叫作 `BUILD_SET` 的字节码来创建集合。
> 






&emsp;
&emsp;
&emsp;
# 6. `dict`和`set`的背后
## 6.1 Python 里的`dict`和`set`的效率有多高？
&emsp;&emsp; 很快。`dict` 的实现是典型的空间换时间：字典类型有着巨大的内存开销，但它们提供了无视数据量大小的快速访问——只要字典能被装在内存里。 正如书中进行的实验，如果把字典的大小从 `1000` 个元素增加到 `10 000 000 `个， 查询时间也不过是原来的 `2.8` 倍， 从 `0.000163`秒增加到了 `0.00456`秒。 这意味着在一个有`1000`万个元素的字典里， 每秒能进行 `200`万个键查询，这样的速度还是很可观的。

## 6.2 散列表(Hash Table，也叫哈希表)的数据结构是怎样的？
&emsp;&emsp; 散列表其实是一个稀疏数组（总是有空白元素的数组称为稀疏数组）。在一般的数据结构教材中，散列表里的单元通常叫作表元（bucket）。在 `dict` 的散列表当中，每个键值对都占用一个表元，每个表元都有两个部分：
> 一个是对键的引用
> 另一个是对值的引用
> 
因为所有表元的大小一致，所以可以通过偏移量来读取某个表元。
&emsp;&emsp; 因为 Python 会设法保证大概还有`1/3`的表元是空的， 所以在快要达到这个阈值的时候， 原有的散列表会被复制到一个更大的空间里面。
如果要把一个对象放入散列表， 那么首先要计算这个元素键的散列值。 Python 中可以用 `hash()` 方法来获取散列值。

## 6.为什么`dict`和`set`是无序的？

## 6.为什么并不是所有的 Python 对象都可以当作 `dict` 的键或 `set` 里的元素？

## 6.为什么 `dict` 的键和 `set` 元素的顺序是跟据它们被添加的次序而定的， 以及为什么在映射对象的生命周期中， 这个顺序并不是一成不变的？

## 6.为什么不应该在迭代循环 `dict` 或是 `set` 的同时往里添加元素？

TODO: 