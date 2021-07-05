# *args 和 **kwargs

## 1. *args 和 **kwargs 是什么？必须怎么写吗？
&emsp;&emsp; `*args` 和`**kwargs`经常在函数的形参列表中看到这两个参数，通过它们你可以将不定数量的参数传递给⼀个函数,**这⾥的不定的意思是**： 预先并不知道, 函数使⽤者会传递多少个参数给你, 所以在这个场景下使⽤这两个关键字。 *args 是⽤来发送⼀个⾮键值对的可变数量的参数列表给⼀个函数
&emsp;&emsp;其实并不是必须写成`*args` 和`**kwargs`。 **只有变量前⾯的 `*`(星号)才是必须的. 你也可以写成`*var` 和`**vars`. ⽽写成`*args` 和`**kwargs`只是⼀个通俗的命名约定**。



&emsp;
## 2. `*args` 的⽤法
&emsp;&emsp;通过`*args`，你可以将不定个参数传给函数，我们来看一个例子：
```python
def test_for_args(f_args, *args):
    print("第一个参数：%s"%f_args)
    i = 0
    for arg in args:
        i = i + 1
        print("*args里的第%d个参数：%s"%(i, arg))

# 调用： 只传一个参数
test_for_args("Hello")
# 输出： 第一个参数：Hello

#调用： 传两个参数
test_for_args("Hello", "World")
# 输出： 
#   第一个参数：Hello
#   *args里的第1个参数：World

#调用： 传五个参数
test_for_args("Hello", "World", "Hello", "China!")
# 输出： 
#   第一个参数：Hello
#   *args里的第1个参数：World 
#   *args里的第2个参数：Hello 
#   *args里的第3个参数：China!
```
从上面的例子中我们可以了解到，假如你想不确定你的函数要接收几个实参，那么你可以使用 `*args`



&emsp;
## 3. `**kwargs`
&emsp;&emsp; 和`*args`相同的是，`**kwargs`也能接收不确定个参数；但和`*args`不同的是，`**kwargs`**接收的是键值对**,作为参数传递给⼀个函数，我们来看一个例子：
```python
def test_for_kwargs(**kwargs):
    for key, value in kwargs.items(): # 注意这里不带星号哦
        print("%s :  %s"%(key, value))

test_for_kwargs(apple="苹果", banana="香蕉", pineapple="菠萝")
# 输出：
#   apple :  苹果
#   banana :  香蕉
#   pineapple :  菠萝
```


### 4. *args 和 **kwargs的其它作用
&emsp;&emsp;除了 在定义函数时 可以使用`*args` 和`**kwargs`外，我们同样可以使⽤*args和**kwargs 来调⽤⼀个函数，来看下面的代码：
**使用`*args`来调用函数：**
```python
def test(arg1, arg2, arg3):
    print("arg1 : %s"%arg1)
    print("arg2 : %s"%arg2)
    print("arg3 : %s"%arg3)

if __name__ == "__main__":
    values = ("一","二","三")
    test(*values)
```
输出：
>arg1 : 一
>arg2 : 二
>arg3 : 三
**使用`**kwargs`来调用函数：**
```python
def test(arg1, arg2, arg3):
    print("arg1 : %s"%arg1)
    print("arg2 : %s"%arg2)
    print("arg3 : %s"%arg3)

if __name__ == "__main__":
    k_values ={"arg3": 'one', "arg2": "two", "arg1": 'three'}
    test(**k_values)
```
输出：
> arg1 : three
> arg2 : two
> arg3 : one
>
仔细看上面的输出，`{"arg3": 'one', "arg2": "two", "arg1": 'three'}`和`(arg1, arg2, arg3)`**是按名字匹配的，而不是按顺序匹配的。**




### 5. 标准参数、`*args`、 `**kwargs`在使⽤时的顺序
&emsp;&emsp;那么如果你想在函数⾥同时使⽤所有这三种参数， 顺序是这样的：
```python
some_func(fargs, *args, **kwargs)
```