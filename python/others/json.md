- [1. 基础](#1-基础)
  - [1.1 什么是Json？](#11-什么是json)
  - [1.2 和XML相比，Json有何优点？](#12-和xml相比json有何优点)
- [2. Json语法](#2-json语法)
- [3. 在python中操作Json](#3-在python中操作json)


&emsp;
&emsp;
# 1. 基础
## 1.1 什么是Json？
&emsp;&emsp; Json的全名为`JavaScript Object Notation`(JavaScript 对象表示法)，是一种用来存储和交换文本信息的语法，作用类似的还有`XML`。

## 1.2 和XML相比，Json有何优点？
① JSON 比 XML 更小、更快，更易解析；
② JSON更加的简洁，可以一眼就看出其中的内容，方便检查排错；
③ 跨平台更好，XML解析成DOM对象的时候，不同的浏览器可能会有差异，而json没有这个问题
④ Json学起来也更容易。



&emsp;
&emsp;
# 2. Json语法
客户端与服务端的交互数据无非就是两种
> 数组
> 对象
> 
比如最简单的这种：`{"name" : "zhuxiao5"}`，跟python里的字典类似，也是一个Json格式的数据，再比如复杂一点的：
```python
{
    "animals": {
        "dog": [
            {
                "name": "Rufus",
                "age":15
            },
            {
                "name": "Marty",
                "age": null
            }
        ]
    }
}
```
针对上面这个Json对象，我们来解析一下：
于是乎，JSON所表示的数据要么就是对象，要么就是数据：
> ① 对象通过键值对表现；
> ② 键通过双引号包裹，后面跟冒号“：”，然后跟该键的值；
> ③ 值可以是字符串、数字、数组等数据类型；
> ④ 对象与对象之间用逗号隔开；
> ⑤ “{}”用来表达对象；
> ⑥ “[]”用来表达数组；
> 



&emsp;
&emsp;
# 3. 在python中操作Json
Python中也自带了Json模块：
```python
import json
```
其中json.dumps()、json.loads()较为常用：
> json.dumps() : python对象 --> json
> json.loads() : python对象 <-- json
> 
比如说：
```python
import json

test = {
    "animals": {
        "dog": [
            {
                "name": "Rufus",
                "age":15
            },
            {
                "name": "Marty",
                "age": None
            }
        ]
    }
}

loaded_data = json.dumps(test)
print(f"Type of loaded_data : {type(loaded_data)}")

dumped_data = json.loads(loaded_data)
print(f"Type of dumped_data : {type(dumped_data)}")
```
运行结果：
```
Type of loaded_data : <class 'str'>
Type of dumped_data : <class 'dict'>
```
在将Json对象转化为Python对象后，我们直接把它当成一个字典处理就行了。
