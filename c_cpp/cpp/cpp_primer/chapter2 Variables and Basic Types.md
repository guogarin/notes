## const的引用 和 普通的引用 在绑定规则上 有何区别？
C++ Primer, Fifth Edition 2.4.1 有详细讲述
我们都知道，引用的类型 必须与 其所所引用对象的类型一致，但在初始化 常量引用时，允许用仍以表达式作为初始化，只要该表达式的结果可以转换成要用类型即可：
```cpp
int i = 42;
const int &r1 = i;  // we can bind a const int& to a plain int object
const int &r2 = 42; // 正确: r1 是一个 常量引用，允许将一个字面值绑定到它上面
int &r3 = 43        // 错误：r3 是一个 普通引用
const int &r4 = r1 * 2; // 正确: r4 is a reference to const
int &r5 = r * 2;        // 错误：r5 是一个 普通引用
```
我们来写代码验证一下：
在下面的代码中，`ref`是一个普通引用：
```cpp
int main()
{
    int &ref = 2; // ref是一个普通引用
    cout << "ref : "<< ref << endl;
    return 0;
}
    
```
编译时报错：
```
test.cpp: In function ‘int main()’:
test.cpp:15:16: error: invalid initialization of non-const reference of type ‘int&’ from an rvalue of type ‘int’
     int &ref = 2;
                ^
```
对上面的代码进行适当的修改：将`reg`改为 `const引用`，顺利通过编译：
```cpp
int main()
{
    const int &ref = 2; // 和上面不一样的是，ref为const引用
    cout << "ref : "<< ref << endl;
    return 0;
}
```
成功通过编译，输出结果：
```
ref : 2
```

## `++`和`--` 的 前缀版本、后缀版本 有何区别？
见《左值、右值、左值引用、右值引用》的笔记
TODO: 可以做一个文件跳转的功能？