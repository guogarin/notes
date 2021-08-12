# 1. `assert()`
## 1.1 `assert()`原理
&emsp;&emsp; 需要注意的是，断言`assert()`并不是一个函数，而是一个宏。程序在假设条件下，能够正常良好的运作，其实就相当于一个 if 语句：
```cpp
int a;
if(假设a为 true，即 a > 0){
    程序正常运行；
}else{ // 假设a为 false，即 a <= 0
    报错&&终止程序！（避免由程序运行引起更大的错误）  
}
```
如果每次都用`if/else`语句来判断的话就太臃肿了。

## 1.2 `assert()`定义在哪里？
```cpp
#include <assert.h>
```

## 1.3 如何使用`assert()`
&emsp;&emsp; `assert()` 是开发阶段和测试阶段用的。比如你一个函数在开发阶段第一行就assert判断下参数（因为有时候仅靠静态期类型检查不足以约束，还有一些运行期才能判断的条件）。返回前再assert判断结果，道理同上。然后在发布代码的时候会关闭Debug开关（`#define NDEBUG`），`assert()`就没了。
我们来测验一下：
```cpp
#include <iostream>
#include <assert.h>

int main()
{
    int a = 0;
    assert(a);
    std::cout << "After assert(a) " << std::endl;
}
```
编译后运行：
```
est.o: test.cpp:8: int main(): Assertion `a' failed.
Aborted (core dumped)
```
可以看到上面的代码直接退出了。我们在`#include <assert.h>`前加入一行`#define NDEBUG`：
```cpp
#include <iostream>
#define NDEBUG // 加了这一行
#include <assert.h>

int main()
{
    int a = 0;
    assert(a);
    std::cout << "After assert(a) " << std::endl;
}
```
编译后运行：
```
After assert(a) 
```
可以看到`assert()`就不起作用了。






&emsp;
&emsp; 
# 2. 