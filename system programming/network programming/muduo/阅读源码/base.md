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






&emsp;
&emsp;
# 2. `Types.h`
## 2.1 总结
&emsp;&emsp; 

## 2.2 分析
```cpp
///
/// The most common stuffs.
///
namespace muduo
{

using std::string;

inline void memZero(void* p, size_t n)
{
  memset(p, 0, n);
}

// Taken from google-protobuf stubs/common.h
//
// Protocol Buffers - Google's data interchange format
// Copyright 2008 Google Inc.  All rights reserved.
// http://code.google.com/p/protobuf/
//
// Redistribution and use in source and binary forms, with or without
// modification, are permitted provided that the following conditions are
// met:
//
//     * Redistributions of source code must retain the above copyright
// notice, this list of conditions and the following disclaimer.
//     * Redistributions in binary form must reproduce the above
// copyright notice, this list of conditions and the following disclaimer
// in the documentation and/or other materials provided with the
// distribution.
//     * Neither the name of Google Inc. nor the names of its
// contributors may be used to endorse or promote products derived from
// this software without specific prior written permission.
//
// THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
// "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
// LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
// A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
// OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
// SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
// LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
// DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
// THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
// (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
// OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

// Author: kenton@google.com (Kenton Varda) and others
//
// Contains basic types and utilities used by the rest of the library.

//
// Use implicit_cast as a safe version of static_cast or const_cast
// for upcasting in the type hierarchy (i.e. casting a pointer to Foo
// to a pointer to SuperclassOfFoo or casting a pointer to Foo to
// a const pointer to Foo).
// When you use implicit_cast, the compiler checks that the cast is safe.
// Such explicit implicit_casts are necessary in surprisingly many
// situations where C++ demands an exact type match instead of an
// argument type convertable to a target type.
//
// The From type can be inferred, so the preferred syntax for using
// implicit_cast is the same as for static_cast etc.:
//
//   implicit_cast<ToType>(expr)
//
// implicit_cast would have been part of the C++ standard library,
// but the proposal was submitted too late.  It will probably make
// its way into the language in the future.
template<typename To, typename From>
inline To implicit_cast(From const &f)
{
  return f;
}

// When you upcast (that is, cast a pointer from type Foo to type
// SuperclassOfFoo), it's fine to use implicit_cast<>, since upcasts
// always succeed.  When you downcast (that is, cast a pointer from
// type Foo to type SubclassOfFoo), static_cast<> isn't safe, because
// how do you know the pointer is really of type SubclassOfFoo?  It
// could be a bare Foo, or of type DifferentSubclassOfFoo.  Thus,
// when you downcast, you should use this macro.  In debug mode, we
// use dynamic_cast<> to double-check the downcast is legal (we die
// if it's not).  In normal mode, we do the efficient static_cast<>
// instead.  Thus, it's important to test in debug mode to make sure
// the cast is legal!
//    This is the only place in the code we should use dynamic_cast<>.
// In particular, you SHOULDN'T be using dynamic_cast<> in order to
// do RTTI (eg code like this:
//    if (dynamic_cast<Subclass1>(foo)) HandleASubclass1Object(foo);
//    if (dynamic_cast<Subclass2>(foo)) HandleASubclass2Object(foo);
// You should design the code some other way not to need this.

template<typename To, typename From>     // use like this: down_cast<T*>(foo);
inline To down_cast(From* f)                     // so we only accept pointers
{
  // Ensures that To is a sub-type of From *.  This test is here only
  // for compile-time type checking, and has no overhead in an
  // optimized build at run-time, as it will be optimized away
  // completely.
  if (false)
  {
    implicit_cast<From*, To>(0);
  }

#if !defined(NDEBUG) && !defined(GOOGLE_PROTOBUF_NO_RTTI)
  assert(f == NULL || dynamic_cast<To>(f) != NULL);  // RTTI: debug mode only!
#endif
  return static_cast<To>(f);
}

}  // namespace muduo

```







# 参考文献
1. [muduo学习笔记](https://blog.csdn.net/qq_39898877/category_10272331.html)
2. [Muduo网络库](https://blog.csdn.net/daaikuaichuan/category_8549087.html?spm=1001.2014.3001.5482)
3. [muduo库](https://blog.csdn.net/wanggao_1990/category_11209321.html)
4. [muduo 库解析之一：Copyable 和 NonCopyable](https://www.cnblogs.com/xiaojianliu/p/14692796.html)
5. [关于copyable和noncopyable的思考](https://zhuanlan.zhihu.com/p/387664658)