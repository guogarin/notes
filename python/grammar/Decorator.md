# 1. 什么是装饰器？
&emsp;&emsp; 顾名思义，装饰器就是用来"装饰"Python的工具，使得代码更具有Python简洁的风格。换句话说，它是一种函数的函数，因为装饰器传入的参数就是一个函数，然后通过实现各种功能来对这个函数的功能进行增强。
**注意：** 传给装饰器的参数是函数。
   





&emsp;
# 2. 为什么要用装饰器？
(1) 授权(Authorization)
&emsp;&emsp; 装饰器能有助于检查某个人是否被授权去使用一个web应用的端点(endpoint)。它们被大量使用于
Flask和Django web框架中。
(2) 日志(Logging)
&emsp;&emsp; 可以通过装饰器记日志，而且还能节省代码量。






&emsp;
# 3. 装饰器的原理是？
## 3.1 装饰器的基础一：python的函数可以像变量一样传递
&emsp;&emsp; 在python中，一切皆对象，Python 中的函数可以像普通变量一样赋给另一个变量，也可以当做参数传递给另外一个函数：
**函数可以像普通变量一样赋给另一个变量**
```python
def hi(name="yasoob"):
    return "hi " + name
 
print(hi())
# output: 'hi yasoob'
 
# 我们甚至可以将一个函数赋值给一个变量，比如
greet = hi
# 注意，这里没有再使用小括号，因为我们并不是在调用hi函数，而是在将它放在 greet变量 里头。
# 我们尝试运行下这个
print(greet())
# output: 'hi yasoob'
 
# 如果我们删掉旧的hi函数，看看会发生什么！
del hi
print(hi())
#outputs: NameError

# 但此时 greet还能调用成功
print(greet())
#outputs: 'hi yasoob'
```
**函数也可以当做参数传递给另外一个函数**
```python
def hi():
    return "hi yasoob!"

# 注意！func参数的类型是一个函数
def doSomethingBeforeHi(func):
    print("I am doing some boring work before executing hi()")
    print(func())
 
# 将 hi函数 作为参数传给 doSomethingBeforeHi函数
doSomethingBeforeHi(hi)
#outputs:I am doing some boring work before executing hi()
#        hi yasoob!
```

## 3.2 装饰器的基础二：在函数中定义函数 和 从函数中返回函数 
&emsp;&emsp; 显然，在python中，函数可以作为变量传递，那么他必然可以作为变量被`return`:
```python
def hi(name="yasoob"):
    def greet():
        return "now you are in the greet() function"
 
    def welcome(): # 在函数中定义函数
        return "now you are in the welcome() function"
 
    if name == "yasoob":
        return greet
    else:
        return welcome
 
a = hi()
print(a)
# outputs: <function greet at 0x7f2143c01500>
# 之所以返回值是这样的，因为上面的返回的是函数

 
# 上面清晰地展示了`a`现在指向到hi()函数中的greet()函数
# 现在试试这个
print(a()) # 调用返回的函数
# outputs: now you are in the greet() function
```

## 3.3 装饰器原理
&emsp;&emsp; 装饰器的原理是基于 `python的函数可以像变量一样传递`，本质上来说，装饰器其实就是 **从函数中返回函数**：我们将 函数A 作为参数传递给 函数B，函数B将函数A`装饰`后再将函数A作为参数返回，这里的装饰可以是 在调用函数A之前做点什么，也可以在调用函数A之后做点什么。我们来看一个例子：
```python
from functools import wraps # 
def decorator_name(f):
    def decoratored(*args, **kwargs):
        if not can_run:
            return "Function will not run"
        else:
            return f(*args, **kwargs)
    
    return decoratored
```
`decorator_name函数`就是一个装饰器，它先判断 `can_run`是否为`True`，是的话就返回`函数f`，不是的话就返回`"Function will not run"`。
**这个装饰器的作用其实就是**：在调用`函数f`**之前**，先判断`can_run`是否为`True`。
**我们现在来分析一下上面的装饰器：**
> **形参f**：`f`的类型是 函数
> **`decoratored函数`** ：它定义在`decorator_name函数`里面，它先判断`can_run`是否为`True`，是的话就返回`函数f`，不是的话就返回`"Function will not run"`，
> **返回值**：该装饰器的返回值是`decoratored函数`，而`decoratored函数`的作用是就是装饰参数f，所以 `decorator_name函数`的作用就是装饰它的 参数f






&emsp;
## 4. 如何使用装饰器？
需要的包：`from functools import wraps`
调用：使用`@`，我们来调用一下前面定义的`decoratored装饰器`：
```python
@decorator_name
def func():
    return("Function is running")
 
can_run = True
print(func()) # can_run为 True时 
# Output: Function is running
 
can_run = False
print(func()) # can_run为 False 时 
# Output: Function will not run
```






&emsp;
# 4. 装饰器的参数
## 4.1 `*args` 和 `**kargs` 是什么？
&emsp; 为了传可变参数个数，我们来看下面的代码：
```python
from functools import wraps

def decorator_name(func):
    @wraps(func)
    def decoratored_func(*args, **kargs):
        print(*args)
        print(kargs)
        return func(*args, **kargs)
    return decoratored_func

@decorator_name
def test(name, age):
    print("My name is %s, I am %d years old."%(name, age))


if __name__ == "__main__":
    test("Jack", 23)
```
输出：
> Jack 23
> {}
> My name is Jack, I am 23 years old.

## 4.2 玩一定要用`*args` 和 `**kargs`，直接写需要的参数名不行吗？
&emsp; &emsp; 可以直接写需要的参数名，但这样就把参数的个数限定死了，在装饰器中使用`*args` 和 `**kwargs` 主要是为了兼容，因为 每个调用装饰器的函数 的形参个数都不一样，这样写兼容性高：
### 4.2.1 只修改`decorator_name装饰器`的形参形式

```python
def decorator_name(func):
    @wraps(func)
    def decoratored_func(arg1, arg2): # 这里没有使用*args和**kargs，而是将参数设为两个
        print(arg1)
        print(arg2)
        return func(arg1, arg2)
    return decoratored_func

@decorator_name
def test(name, age):
    print("My name is %s, I am %d years old."%(name, age))

if __name__ == "__main__":
    test("Jack", 23)
```
输出：
> Jack
> 23
> My name is Jack, I am 23 years old.
> 
**结论：代码正常运行，说明确实可以将装饰器的参数个数写成确定的个数。**

### 4.2.2 修改`decorator_name装饰器`的形参形式，test()函数改为接收三个参数
我们把上面的代码改一下：`decorator_name装饰器`直接把参数设为两个，test函数改为接收三个参数
```python
from functools import wraps

def decorator_name(func):
    @wraps(func)
    def decoratored_func(arg1, arg2):# 这里没有使用*args和**kargs，而是将参数设为两个
        print(arg1)
        print(arg2)
        return func(arg1, arg2)
    return decoratored_func

@decorator_name
def test(name, age, sex): # test函数接收三个参数
    print("My name is %s, I am %d years old."%(name, age))

if __name__ == "__main__": # 传三个参数给test函数
    test("Jack", 23, '男')
```
输出：
>Traceback (most recent call last):
>&emsp;  File "d:/code_practice/test.py", line 17, in <module>
>&emsp;&emsp;    test("Jack", 23, '男')
>TypeError: decoratored_func() takes 2 positional arguments but 3 were given
>
**代码报错，将装饰器的参数个数写成确定的个数会影响兼容性，因为你很难确定调用装饰器的函数接收几个参数，因此将装饰器的形成设为`*args` 和 `**kargs`会比较合理，不管调用装饰器的函数接收几个参数都不会报错。**

## 4.3 如何编写带参数的装饰器？如何调用？
&emsp;&emsp; 我们在调用装饰器的时候虽然没有显示的传参进去，但是其实这里我们隐式的传了一个参数进去：调用该装饰器的函数。
我们来看一个 用来记录日志的 装饰器`logit`：
```python
from functools import wraps
# 注意看，里面嵌套了 三层 ！三层！三层！
def logit(logfile='out.log'):
    def logging_decorator(func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            # 打开logfile，并写入内容
            with open(logfile, 'a') as opened_file:
                # 现在将日志打到指定的logfile
                opened_file.write(log_string + '\n')
            return func(*args, **kwargs)
        return wrapped_function
    return logging_decorator
```
我们可以看到 装饰器`logit` 接收一个参数`logfile`，这个参数有默认实参'out.log'，我们来调用一下：
```python
@logit() # 没有传 logfile进去，使用的是 默认实参 out.log
def myfunc1():
    pass
 
myfunc1()
# Output: myfunc1 was called
# 现在一个叫做 out.log 的文件出现了，里面的内容就是上面的字符串
 

@logit(logfile='func2.log') # 这里传了 func2.log 进去
def myfunc2():
    pass
 
myfunc2()
# Output: myfunc2 was called
# 现在一个叫做 func2.log 的文件出现了，里面的内容就是上面的字符串
```

| Column A                      | Column B                                         |
| ----------------------------- | ------------------------------------------------ |
| `@logit`                      | 只有一个参数：调用`logit装饰器`的函数            |
| `@logit(logfile='func2.log')` | 两个参数：① 调用`logit装饰器`的函数；② `logfile` |


# 参考文献
1. [Python 函数装饰器](https://www.runoob.com/w3cnote/python-func-decorators.html)