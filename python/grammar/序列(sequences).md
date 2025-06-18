[toc]

# 1. 综合
## 1.1 如何判断一个类型是否属于序列？

## 1.2 哪些标准类型属于序列？

## 1.3 如何判断一个元组是否包含可变类型的数据？
对这个元组使用`hash()`即可，若元组包含 可变类型，则对其使用`hash()`会报错：
```py
>>> l = (1, 2, [3, 4, 5])
>>> l
(1, 2, [3, 4, 5])

>>> type(l)
<class 'tuple'>

>>> hash(l)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```


# 序列的运算
## 序列的乘法

## 当列表中包含可变类型的对象时，对该列表使用乘法可能导致bug的产生
&emsp;&emsp; 若列表中包含可变类型的对象，对该列表使用乘法时，产生的新列表包含的是对该可变对象的引用，而不是拷贝。示例如下：
**(1) 列表推导**
列表推导产生的新列表包含的是对该可变对象的拷贝：
```py
>>> board = [ ['_'] * 3 for i in range(3)]
>>> board
[['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]
>>> board[1][2] = 'XYZ'
>>> board
[['_', '_', '_'], ['_', '_', 'XYZ'], ['_', '_', '_']]
```
**(2) 列表乘法**
列表乘法产生的新列表包含的是对该可变对象的引用，而不是拷贝：
```py
>>> weird_board = [['_'] * 3 ] * 3
>>> weird_board
[['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]
>>> weird_board[1][2] = 'XYZ'
>>> weird_board
[['_', '_', 'XYZ'], ['_', '_', 'XYZ'], ['_', '_', 'XYZ']]
```

```py
# (1) 上面的列表推导 相当于 下面的代码：
board = []
for i in range(3):
    row = ['_'] * 3  # Each iteration builds a new row and appends it to board.
    board.append(row)

# (2) 上面的列表乘法 相当于 下面的代码：
row = ['_'] * 3
weird_board = []
for i in range(3):
    weird_board.append(row) # The same row is appended three times to board.
```


# 标准库的排序算法`sorted()` 和 列表自带的类方法`list.sort`
## `sorted()` 和 `list.sort` 的主要区别是？
`sorted()`会产生一个新列表并将其返回；
`list.sort`会就地将目标列表排序，然后返回一个`None`，不会产生新列表
```py
>>> fruits = ['grape', 'raspberry', 'apple', 'banana']

>>> sorted(fruits)
['apple', 'banana', 'grape', 'raspberry']
>>> fruits
['grape', 'raspberry', 'apple', 'banana']

>>> fruits.sort()
>>> fruits
['apple', 'banana', 'grape', 'raspberry']
```

## `sorted()` 和 `list.sort`的 可选参数
Both `list.sort` and built-in function `sorted` take two optional, keyword-only arguments:
`reverse`
> If True, the items are returned in descending order (i.e., by reversing the comparison of the items). The default is False.
> 
`key`
> A one-argument function that will be applied to each item to produce its sorting key. For example, when sorting a list of strings, key=str.lower can be used to perform a case-insensitive sort, and key=len will sort the strings by character length. The default is the identity function (i.e., the items themselves are compared).
>

```py
>>> fruits = ['grape', 'raspberry', 'apple', 'banana']

# 倒序
>>> sorted(fruits, reverse=True)
['raspberry', 'grape', 'banana', 'apple']

# 按长度排序
>>> sorted(fruits, key=len)
['grape', 'apple', 'banana', 'raspberry']

# 按字符串的第二个字符排序
>>> sorted(fruits, key=lambda x : x[1])
['raspberry', 'banana', 'apple', 'grape']
```

## 如何对一个包含多个不同类型的列表进行排序？
### 这种情况会触发的问题
如果一个列表同时包含`number`和`str`，直接对其排序是不行的：
```py
>>> l = [28, 14, '28', 5, '9', '1', 0, 6, '23', 19]
>>> sorted(l)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '<' not supported between instances of 'str' and 'int'
```
此时可以使用`key`关键字来指定排序规则：
```py
>>> sorted(l, key=int)
[0, '1', 5, 6, '9', 14, 19, '23', 28, '28']

>>> sorted(l, key=str)
[0, '1', 14, 19, '23', 28, '28', 5, 6, '9']
```




# When a List Is Not the Answer
| 类型                      | 适用的场景                                                |
| ------------------------- | --------------------------------------------------------- |
| `arrays`                  | 如果需要保存的是同一个类型的元素，那适合使用`array.array` |
| `Memory Views`            | 避免拷贝，提高效率                                        |
| `NumPy`                   | 处理多维数组很方便                                        |
| `Deques and Other Queues` | 其实就是队列                                              |

