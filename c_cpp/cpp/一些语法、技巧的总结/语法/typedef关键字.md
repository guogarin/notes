# `typedef` 
## 1. 语法规则
```cpp
typedef target other_name; // other_name 是 target 的别名
```
再来看下面的代码：
```cpp
typedef char * p_char;
```
编译器会将 `char *`视为整体，`p_char`视为另一个整体。简单的来说，编译器会将 **最右边那个** 作为别名，而将 `typedef`到 倒数第二个 视为“被代表”的那个(不管它有多长)，我们再来看下面的代码：
```cpp
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} Book;

Book book;
```
在上面的代码中，`Book`是 
```cpp
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} 
```
的别名。
## 和 `#define`对比
```cpp
typedef typename std::vector<T>::size_type size_type;
```
https://feihu.me/blog/2014/the-origin-and-usage-of-typename/

### `typedef` 和 类模板