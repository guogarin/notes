## nonlocal
### 1. 为什么要引入 nonlocal关键字
&emsp;&emsp;我们都知道在Python2中可以在函数里面可以用关键字 global 声明某个变量为全局变量，但是在嵌套函数中，但想要给一个变量声明为非局部变量是没法实现的
&emsp;&emsp;nonlocal是在Python3.2之后引入的一个关键字，它是用在封装函数中的，使得非局部变量成为可能。

### 2. nonlocal的作用
#### 2.1 使用方式
&emsp;&emsp;nonlocal关键字修饰变量后标识该变量是上一级函数中的局部变量，如果上一级函数中不存在该局部变量，nonlocal位置会发生错误（最上层的函数使用nonlocal修饰变量必定会报错）
```python
count = 1

def func():
    count = 'count inside of the func'
    def func_2():
        nonlocal count # 引用到的是func()函数里面的count变量
        print(count)
        count = 2
    func_2()
    print(count)

if __name__ == "__main__":
    func()
    print(count)

```
输出：
> count inside of the func
2
1

**下面我们将 `nonlocal count` 注释掉：**
```python
count = 1

def func():
    count = 'count inside of the func'
    def func_2():
        # nonlocal count # 引用到的是func()函数里面的count变量
        print(count)
        count = 2
    func_2()
    print(count)

if __name__ == "__main__":
    func()
    print(count)

```
输出：
>  File "d:/code_practice/practice.py", line 7
    nonlocal count
    ^
SyntaxError: no binding for nonlocal 'count' found 

**为什么会有这样的错误呢？** 
&emsp;&emsp; 是因为 nonlocal关键字 只对本地变量起作用，而count是全局变量，所以报错，如果想使用全局变量count，应该使用 global关键字：
```python
count = 1

def func():
    # count = 'count inside of the func'
    def func_2():
        global count
        print(count)
        count = 2
    func_2()
    print(count)

if __name__ == "__main__":
    func()
    print(count)
```
输出：
> 1
2
2

我们再来看下面的代码：
```python
count = 1

def func():
    global count 
    count= 'count inside of the func'
    def func_2():
        nonlocal count
        print(count)
        count = 2
    func_2()
    print(count)

if __name__ == "__main__":
    func()
    print(count)
```
上面的代码看起来被没有什么错，但是输出结果为：
> File "d:/code_practice/practice.py", line 8
&emsp;nonlocal count
&emsp;^
&emsp;SyntaxError: no binding for nonlocal 'count' found

**为什么会报错呢？**是因为nonlocal关键字修饰变量后标识该变量是上一级函数中的局部变量，如果上一级函数中不存在该局部变量，nonlocal位置会发生错误，在上面的代码中，count变量是global的，在func_2的上层函数func中被没有一个局部变量叫count，因此nonlocal没有在上一级函数中找到对应的count，所以报错。



### nonlocal 和 global 的区别
1. 两者的功能不同。global关键字修饰变量后标识该变量是全局变量，对该变量进行修改就是修改全局变量，而nonlocal关键字修饰变量后标识该变量是上一级函数中的局部变量，如果上一级函数中不存在该局部变量，nonlocal位置会发生错误（最上层的函数使用nonlocal修饰变量必定会报错）。

2. 两者使用的范围不同。global关键字可以用在任何地方，包括最上层函数中和嵌套函数中，即使之前未定义该变量，global修饰后也可以直接使用，而nonlocal关键字只能用于嵌套函数中，并且外层函数中定义了相应的局部变量，否则会发生错误
