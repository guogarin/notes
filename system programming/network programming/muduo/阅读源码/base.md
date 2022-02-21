# 前言
&emsp;&emsp; 作者没有在《Linux多线程服务端编程 使用muduo C++ 网络库》中对`base`库进行介绍，为了彻底弄懂muduo库，必须自己把它啃下来。





&emsp;
&emsp;
# 1. `copyable.h` 和 `noncopyable.h`
## 1.1 总结
muduo 中的大多数 `class`都是不可拷贝的，有小部分是可拷贝的。
不可拷贝的 `class` 将继承 类`noncopyable`
可拷贝的 `class` 将继承 类`copyable`

## 1.2 代码解读
`copyable.h`中定义了一个`copyable`类，代码如下：
```cpp
namespace muduo{

    // A tag class emphasises the objects are copyable.（用来标识对象可拷贝）
    // The empty base class optimization applies.（EBO(Empty Base Class Optimization)空基类优化）
    // Any derived class of copyable should be a value type.（任何copyable的子类都应该为值类型）
    class copyable{
    protected:
        // 在构造函数和析构函数定义后面加上 =default，显示的要求编译器生成默认的构造和析构函数
        copyable() = default;
        ~copyable() = default;
    };


    class noncopyable{
    public:
        // C++11出现后，我们使用=delete来阻止拷贝，之前是将拷贝构造函数、拷贝运算符定义为private来做到
        noncopyable(const noncopyable&) = delete;
        void operator=(const noncopyable&) = delete;
    protected:
        noncopyable() = default;
        ~noncopyable() = default;
    };

}  // namespace muduo
```

## 参考文献
1. [muduo 库解析之一：Copyable 和 NonCopyable](https://www.cnblogs.com/xiaojianliu/p/14692796.html)
2. [关于copyable和noncopyable的思考](https://zhuanlan.zhihu.com/p/387664658)





&emsp;
&emsp;
# 2. `Types.h`
## 2.1 总结
### 2.1.1 `Types.h`中几个函数的作用
&emsp;&emsp; 
`implicit_cast` 向上转换，即子类转成父类，这一般没有问题，因为父类的行为都包含在子类。
`down_cast` 向下转换，有可能会出现问题，编译时可能不会发现。

 

## 2.2 分析
```cpp
///
/// The most common stuffs.
///
namespace muduo{

    using std::string;

    // 简化了memset()的使用
    inline void memZero(void* p, size_t n){
        memset(p, 0, n);
    }


    // 显然，这是个隐式转换，它将 传进来的参数(即From类型) 转换为 返回值类型(即To类型)
    template<typename To, typename From>
    inline To implicit_cast(From const &f){
        return f;
    }


    // 
    template<typename To, typename From>     
    inline To down_cast(From* f){
        if (false){
            implicit_cast<From*, To>(0);
        }

    #if !defined(NDEBUG) && !defined(GOOGLE_PROTOBUF_NO_RTTI)
        assert(f == NULL || dynamic_cast<To>(f) != NULL);  // RTTI: debug mode only!
    #endif
        return static_cast<To>(f);
    }

}  // namespace muduo
```

## 参考文献
1. [muduo网络库学习笔记（二）：base库之 Types.h](https://www.codeleading.com/article/34434374043/)
2. [c++小技巧(三)更好的类型转换implicit_cast和down_cast](https://blog.csdn.net/xiaoc_fantasy/article/details/79570788)
3. [muduo type.h 解析 C++类型转换技巧](https://blog.csdn.net/weixin_40021744/article/details/88802969)
4. [【muduo】base库之 Types](https://blog.csdn.net/qq_34201858/article/details/104908916)
5. [为什么说不要使用 dynamic_cast，需要运行时确定类型信息，说明设计有缺陷？](https://www.zhihu.com/question/22445339)
6. [implicit_cast与down_cast的使用](https://blog.csdn.net/qq_34400232/article/details/119081262)
7. [implicit_cast与down_cast](https://www.52pojie.cn/thread-1349334-1-1.html)






# 参考文献
1. [muduo学习笔记](https://blog.csdn.net/qq_39898877/category_10272331.html)
2. [Muduo网络库](https://blog.csdn.net/daaikuaichuan/category_8549087.html?spm=1001.2014.3001.5482)
3. [muduo库](https://blog.csdn.net/wanggao_1990/category_11209321.html)
