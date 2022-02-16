# 前言
&emsp;&emsp; 作者没有在《Linux多线程服务端编程 使用muduo C++ 网络库》中对`base`库进行介绍，为了彻底弄懂muduo库，必须自己把它啃下来。





&emsp;
&emsp;
# 1. `copyable.h` 和 `noncopyable.h`
## 1.1 

## 1.2 `copyable.h`
`copyable.h`中定义了一个`copyable`类，代码如下：
```cpp
namespace muduo{

    // A tag class emphasises the objects are copyable.（用来标识对象可拷贝）
    // The empty base class optimization applies.（EBO(Empty Base Class Optimization)空基类优化）
    // Any derived class of copyable should be a value type.（任何copyable的子类都应该为值类型）
    class copyable{
    protected:
        copyable() = default;
        ~copyable() = default;
};


class noncopyable{
public:
    noncopyable(const noncopyable&) = delete;
    void operator=(const noncopyable&) = delete;

protected:
    noncopyable() = default;
    ~noncopyable() = default;
};

}  // namespace muduo
```

## 1.3 `noncopyable.h`






# 参考文献
1. [muduo学习笔记](https://blog.csdn.net/qq_39898877/category_10272331.html)
2. [Muduo网络库](https://blog.csdn.net/daaikuaichuan/category_8549087.html?spm=1001.2014.3001.5482)
3. [muduo库](https://blog.csdn.net/wanggao_1990/category_11209321.html)
4. [muduo 库解析之一：Copyable 和 NonCopyable](https://www.cnblogs.com/xiaojianliu/p/14692796.html)
5. [关于copyable和noncopyable的思考](https://zhuanlan.zhihu.com/p/387664658)