# 1. 什么是POD类型？
&emsp;&emsp; POD 是 Plain Old Data 的缩写，是 C++ 定义的一类数据结构概念。


# 2. 为什么需要POD类型？
&emsp;&emsp;很久很久以前，C 语言统一了江湖。几乎所有的系统底层都是用 C 写的，当时定义的基本数据类型有 int、char、float 等整数类型、浮点类型、枚举、void、指针、数组、结构等等。然后只要碰到一串 01010110010 之类的数据，编译器都可以正确的把它解析出来。
&emsp;&emsp;那么到了 C++ 诞生之后，出现了继承、派生这样新的概念，于是就诞生了一些新的数据结构。比如某个派生类，C 语言中哪有派生的概念啊，遇到这种数据编译器就不认识了。可是我们的计算机世界里，主要的系统还是用 C 写的啊，为了和旧的 C 数据相兼容，C++ 就提出了 POD 数据结构概念。
&emsp;&emsp;POD 是 Plain Old Data 的缩写，是 C++ 定义的一类数据结构概念，比如 int、float 等都是 POD 类型的。Plain 代表它是一个普通类型，Old 代表它是旧的，与几十年前的 C 语言兼容，那么就意味着可以使用 memcpy() 这种最原始的函数进行操作。两个系统进行交换数据，如果没有办法对数据进行语义检查和解释，那就只能以非常底层的数据形式进行交互，而拥有 POD 特征的类或者结构体通过二进制拷贝后依然能保持数据结构不变。也就是说，能用 C 的 memcpy() 等函数进行操作的类、结构体就是 POD 类型的数据。

# 3. 如何判断是否 POD类型？
&emsp;&emsp; 可以用 `is_pod<T>::value` 来判断


# 4. 什么样的类型是 POD类型？
以下类型是POD类型：
> (1) 标量类型（scalar type）
> (2) POD 类
> (3) 以上类型的数组和 cv 限定版本
> 
**什么是 标量类型？**
标量类型是以下类型的统称：
> 算术（整数、浮点）类型（注意，`char`也是整形的一种）
> 枚举类型
> 指针类型
> 指向成员指针类型
> std::nullptr_t 类型
> 以上类型 cv 限定版本
> 

**什么是POD类？**
POD 类是以下类型的统称：
POD struct
POD union
POD struct 和 POD union 的要求：
平凡类（trivial class）
标准布局类（standard-layout class）
无非 POD 类类型或其数组的非静态数据成员
两者区别是一个是非联合类（class/struct），另一个是 union

TODO: 还是没彻底弄明白，只要把下面几篇文章看完就差不多了

# 参考文献
1. [c++11 pod类型(了解)](https://www.cnblogs.com/zzyoucan/p/3918614.html)
2. [整理一下 C++ POD 类型的细节（一）](https://zhuanlan.zhihu.com/p/29734547)
3. [什么是 POD 数据类型？](https://zhuanlan.zhihu.com/p/45545035)
4. [普通、标准布局、POD 和文本类型](https://docs.microsoft.com/zh-cn/cpp/cpp/trivial-standard-layout-and-pod-types?view=msvc-160)