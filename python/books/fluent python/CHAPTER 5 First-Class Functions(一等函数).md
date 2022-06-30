- [1. 一等对象(first-class object)](#1-一等对象first-class-object)
  - [1.1 什么是一等对象？](#11-什么是一等对象)
  - [1.2 python中有哪些是一等对象？](#12-python中有哪些是一等对象)
  - [1.3 一等函数是什么？](#13-一等函数是什么)
- [2. 高阶函数（higher-order function）](#2-高阶函数higher-order-function)
  - [2.1 什么是高阶函数？](#21-什么是高阶函数)
  - [2.2 python有哪些常见的高阶函数？](#22-python有哪些常见的高阶函数)
  - [2.3](#23)
- [3. 支持函数式编程的包(Packages for Functional Programming)](#3-支持函数式编程的包packages-for-functional-programming)
  - [3.1 `operator`模块](#31-operator模块)
    - [3.1.1 `operator`模块中的运算符来取代匿名函数](#311-operator模块中的运算符来取代匿名函数)
    - [3.1.2 `operator`模块中`itemgetter`和`attrgetter`](#312-operator模块中itemgetter和attrgetter)
      - [(1) `itemgetter`和`attrgetter`的作用](#1-itemgetter和attrgetter的作用)
      - [(2) `itemgetter`和`attrgetter`的应用](#2-itemgetter和attrgetter的应用)
  - [3.2 `functools.partial`冻结参数(Freezing Arguments with functools.partial)](#32-functoolspartial冻结参数freezing-arguments-with-functoolspartial)
    - [`functools.partial`的作用](#functoolspartial的作用)
    - [使用](#使用)






&emsp;
&emsp;
&emsp; 
# 1. 一等对象(first-class object)
## 1.1 什么是一等对象？
编程语言理论家把**一等对象**定义为满足下述条件的程序实体：
> ① Created at runtime(在运行时创建)
> ② Assigned to a variable or element in a data structure(能赋值给变量或数据结构中的元素)
> ③ Passed as an argument to a function(能作为参数传给函数)
> ④ Returned as the result of a function(能作为函数的返回结果)
> 
## 1.2 python中有哪些是一等对象？
&emsp;&emsp; 整数、字符串和字典都是一等对象，而且在 Python中，函数也一样是一等对象。

## 1.3 一等函数是什么？
> &emsp;&emsp;The term(术语) “first-class functions” is widely used as shorthand for“functions as first-class objects.” It’s not perfect because it seemsto imply an “elite” among functions. In Python, all functions arefirst-class.
> &emsp;&emsp; 人们经常将“把函数视作一等对象”简称为“一等函数”。 这样说并不完美， 似乎表明这是函数中的特殊群体。 在 Python 中， 所有函数都是一等对象。
> 






&emsp;
&emsp;
&emsp; 
# 2. 高阶函数（higher-order function） 
## 2.1 什么是高阶函数？
&emsp;&emsp; 接受函数为参数，或者把函数作为结果返回的函数是高阶函数（higher-order function）。

## 2.2 python有哪些常见的高阶函数？
&emsp;&emsp; 比如`map()` 和 `filter()`。

## 2.3 







&emsp;
&emsp;
&emsp; 
# 3. 支持函数式编程的包(Packages for Functional Programming)
&emsp;&emsp; 虽然 Guido 明确表明，Python 的目标不是变成函数式编程语言，但是得益于 `operator` 和 `functools` 等包的支持， 函数式编程风格也可以信手拈来。
## 3.1 `operator`模块
### 3.1.1 `operator`模块中的运算符来取代匿名函数
&emsp;&emsp; 在函数式编程中，经常需要把算术运算符当作函数使用。例如，不使用递归计算阶乘(factorials)。求和可以使用 `sum`函数，但是求积则没有这样的函数。我们可以使用 `reduce` 函数，但是需要一个函数计算序列中两个元素之积。
(1) 使用`reduce` 函数和一个匿名函数计算阶乘:
```python
from functools import reduce

# factorial n 阶乘
def fact(n):
    return reduce(lambda a,b : a*b, range(1, n+1))
```
(2) 使用`operator`模块
&emsp;&emsp; `operator` 模块为多个算术运算符提供了对应的函数，从而避免编写 `lambda a, b: a*b` 这种平凡的匿名函数。 使用算术运算符函数，可以把上面的示例改写为：
```python
from functools import reduce
from operator import mul

def fact(n):
    # 用 operator.mul 函数改写阶乘
    return reduce(mul, range(1, n+1))
```

### 3.1.2 `operator`模块中`itemgetter`和`attrgetter`
#### (1) `itemgetter`和`attrgetter`的作用
> ① `itemgetter` ： `itemgetter`会自行构建函数，它创建的函数会根据**索引**提取对象的属性
> ② `attrgetter` ： `attrgetter`和`itemgetter`作用类似，它创建的函数根据**名称**提取对象的属性。
>
```python
from operator import itemgetter

metro_data = [
    ['Tokyo', 'JP', 36.933, (35.689722, 139.691667)],
    ['Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)],
    ['Mexico City', 'MX', 20.142, (19.433333, -99.133333)],
    ['New York-Newark', 'US', 20.104, (40.808611, -74.020386)],
    ['Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)],
]

# itemgetter(1)返回的是函数
f = itemgetter(1)

# 将itemgetter(1)的返回值传给map()
print(list(map(f, metro_data)))
```
运行结果：
```
['JP', 'IN', 'MX', 'US', 'BR']
```
**注意，如果指定了多个条目，它俩都会返回一个查找值的元组**
```python
# 得到一个函数
cc_name = itemgetter(1, 0) 
for city in metro_data:
    print(cc_name(city)) # 调用函数
```
运行结果：
```
('JP', 'Tokyo')
('IN', 'Delhi NCR')
('MX', 'Mexico City')
('US', 'New York-Newark')
('BR', 'Sao Paulo')
```

#### (2) `itemgetter`和`attrgetter`的应用
它俩可以用来配合 `sorted()`的`key`参数 来进行排序：
```python
metro_data = [
    ['Tokyo', 'JP', 36.933, (35.689722, 139.691667)],
    ['Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)],
    ['Mexico City', 'MX', 20.142, (19.433333, -99.133333)],
    ['New York-Newark', 'US', 20.104, (40.808611, -74.020386)],
    ['Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)],
]

# 以第2个元素来排序
for city in sorted(metro_data, key=itemgetter(1)):
    print(city)
```
运行结果：
```
['Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)]
['Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)]
['Tokyo', 'JP', 36.933, (35.689722, 139.691667)]
['Mexico City', 'MX', 20.142, (19.433333, -99.133333)]
['New York-Newark', 'US', 20.104, (40.808611, -74.020386)]
```
**如果想多标准排序，可以指定几个参数，让`itemgetter`返回一个元组，依次完成多标准排序**


## 3.2 `functools.partial`冻结参数(Freezing Arguments with functools.partial)
### `functools.partial`的作用
&emsp;&emsp; `functools.partial`这个高阶函数用于部分应用一个函数。 部分应用是指， 基于一个函数创建一个新的可调用对象， 把原函数的某些参数固定。 使用这个函数可以把接受一个或多个参数的函数改编成需要回调的 API， 这样参数更少。 

### 使用
```python
from operator import mul
from functools import partial

# 得到一个函数
triple = partial(mul, 3)

print( triple(8) )

print( list( map(triple, range(6)) ) )
```
运行结果：
```
24
[0, 3, 6, 9, 12, 15]
```
