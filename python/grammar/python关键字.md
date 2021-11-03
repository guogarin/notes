# 1.  `global` 和 `nonlocal`
## 1.1 为什么需要 `global` 和 `nonlocal` 关键字？他俩是用来解决什么问题的？
```python
def func():
    count = '我是func()里的局部变量count'
    def func_1():
        print(count)
    func_1()

if __name__ == "__main__":
    func()
```
输出：
```
Traceback (most recent call last):
  File "d:/code_practice/practice.py", line 9, in <module>
    func()
  File "d:/code_practice/practice.py", line 6, in func
    func_1()
  File "d:/code_practice/practice.py", line 4, in func_1
    print(count)
UnboundLocalError: local variable 'count' referenced before assignment
```
**结果分析：** 
&emsp;&emsp; 在`func_1()`中访问外层的`count`变量 时报错了，因为在`func_1()`的作用域内没有定义`count`变量，我们需要显式的告诉解释器，我们希望访问的是外面的`count`变量，而`global` 和 `nonlocal`正是用来完成这个工作的。

## 1.2 `global` 和 `nonlocal`的作用分别是什么？
| 关键字     | 作用                                                 |
| ---------- | ---------------------------------------------------- |
| `global`   | 用来在 函数或其它局部作用域中 使用 **全局变量**      |
| `nonlocal` | 用来在 函数或其它作用域中 使用上层的 **非全局变量**) |
```python
count = "我是全局变量count"

def func():
    count = '我是func()里的局部变量count'
    def func_1():
        global count 
        print("global count  : ", count)
    def func_2():
        nonlocal count 
        print("nonlocal count: ", count)
    func_1()
    func_2()

if __name__ == "__main__":
    func()
```
运行结果：
```
global count  :  我是全局变量count
nonlocal count:  我是func()里的局部变量count
```
**结果分析：**
&emsp;&emsp; 使用`global`修饰的`count` 引用到的是 全局变量`count`；
&emsp;&emsp; 使用`nonlocal`修饰的`count` 引用到的是 局部变量`count`；
这两个关键字的作用一目了然。

## 1.3 `global` 可以作用于哪些变量？
`global`只能作用于全局变量
```python
def func():
    count = '我是func()里的局部变量count'
    def func_1():
        global count
        print(count)  
    func_1()

if __name__ == "__main__":
    func()
```
运行结果：
```
Traceback (most recent call last):
  File "d:/code_practice/practice.py", line 9, in <module>
    func()
  File "d:/code_practice/practice.py", line 6, in func
    func_1()
  File "d:/code_practice/practice.py", line 5, in func_1
    print(count)
NameError: name 'count' is not defined
```
**结果分析：**
&emsp;&emsp; 在上面的代码中，我们没有定义全局的`count`变量，虽然`global count`没有报错，但是我们在访问`count`时(`print(count)`)报错了，也证实了 `global`只能作用于全局变量。

## 1.4 `nonlocal`可以作用于哪些变量？
`nonlocal`只能用来在 函数或其它作用域中 使用上层的 **非全局变量**)，而且外层必须定义了局部变量，要不然会报错：
```python
count = "我是全局变量count"

def func():
    # count = '我是func()里的局部变量count'
    def func_1():
        nonlocal count
        print(count)  
    func_1()

if __name__ == "__main__":
    func()
```
运行结果：
```
  File "d:/code_practice/practice.py", line 6
    nonlocal count
    ^
SyntaxError: no binding for nonlocal 'count' found
```
**结果分析：**
&emsp;&emsp; 因为 我们没有定义 局部变量`count`，只有一个全局变量`count`，所以会报错。

## 1.5 `nonlocal` 和 `global` 的区别
**① 两者的功能不同**
> &emsp;&emsp; global关键字修饰变量后标识该变量是全局变量，对该变量进行修改就是修改全局变量，而nonlocal关键字修饰变量后标识该变量是上一级函数中的局部变量，如果上一级函数中不存在该局部变量，nonlocal位置会发生错误（最上层的函数使用nonlocal修饰变量必定会报错）。
> 
**② 两者使用的范围不同**
> &emsp;&emsp; global关键字可以用在任何地方，包括最上层函数中和嵌套函数中，即使之前未定义该变量，global修饰后也可以直接使用，而nonlocal关键字**只能用于嵌套函数中**，并且外层函数中定义了相应的局部变量，否则会发生错误
> 
