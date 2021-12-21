- [一、Pythonic Thinking](#一pythonic-thinking)
  - [Item 1：Know Which Version of Python You’re Using](#item-1know-which-version-of-python-youre-using)
    - [1. 为什么要确定python的版本？](#1-为什么要确定python的版本)
    - [2. 有哪些方法可以获取python的版本？](#2-有哪些方法可以获取python的版本)
  - [Item 2：Follow the PEP 8 Style Guide](#item-2follow-the-pep-8-style-guide)
    - [1. 什么是 PEP 8？](#1-什么是-pep-8)
  - [Item 3：Know the Differences Between bytes and str](#item-3know-the-differences-between-bytes-and-str)
    - [1. 如何定义`bytes`和`str`？](#1-如何定义bytes和str)
    - [2. `python`的`bytes`和`str` 分别表示的是什么？](#2-python的bytes和str-分别表示的是什么)
      - [2.1 `bytes`](#21-bytes)
      - [2.2 `str`](#22-str)
    - [3.  `bytes`和`str`之间如何转换？](#3--bytes和str之间如何转换)
    - [4. 对于`bytes`数据，为什么 `print()`和`list()`出来的数据不一样呢？](#4-对于bytes数据为什么-print和list出来的数据不一样呢)
    - [5. `bytes`和`str`的兼容性](#5-bytes和str的兼容性)
    - [6. 读写文件](#6-读写文件)
      - [6.1 写文件需要遵守的方针](#61-写文件需要遵守的方针)
      - [6.2 写文件](#62-写文件)
      - [6.3 读文件](#63-读文件)
    - [7. 总结](#7-总结)
  - [Item 4：Prefer Interpolated F-Strings Over C-style Format Strings and str.format](#item-4prefer-interpolated-f-strings-over-c-style-format-strings-and-strformat)
    - [1. Python有哪些格式化字符串的方法？](#1-python有哪些格式化字符串的方法)
      - [1.1 方法一：使用 `%` 来格式化 C风格字符串](#11-方法一使用--来格式化-c风格字符串)
        - [1.1.1 优点](#111-优点)
        - [1.1.2 缺点：](#112-缺点)
    - [1.2 方法二：使用`dic`来格式化 `%` 来格式化 C风格字符串](#12-方法二使用dic来格式化--来格式化-c风格字符串)
      - [1.2.1 优点](#121-优点)
      - [1.2.2 缺点](#122-缺点)
    - [1.3 方法三：使用内置的`str`类的`format()`方法](#13-方法三使用内置的str类的format方法)
    - [1.4 方法四：使用 插值格式字符串(interpolated format string，简称f-string)](#14-方法四使用-插值格式字符串interpolated-format-string简称f-string)
      - [1.4.1 怎么使用 插值格式字符串？](#141-怎么使用-插值格式字符串)
      - [1.4.2 `str.format()`方法 和 `f-string`的联系](#142-strformat方法-和-f-string的联系)
      - [1.4.3 插值格式字符串 好在哪？](#143-插值格式字符串-好在哪)
  - [Item 5：Write Helper Functions Instead of Complex Expressions(用辅助函数取代复杂的表达式)](#item-5write-helper-functions-instead-of-complex-expressions用辅助函数取代复杂的表达式)
    - [1. 这条规则指的是？](#1-这条规则指的是)
    - [2. 为什么？](#2-为什么)
  - [Item 6: Prefer Multiple Assignment Unpacking Over Indexing(把数据直接解包到多个变量里，不要通过下标范围)](#item-6-prefer-multiple-assignment-unpacking-over-indexing把数据直接解包到多个变量里不要通过下标范围)
    - [1. 为什么不建议使用下标访问？](#1-为什么不建议使用下标访问)
  - [Item 7:  Prefer enumerate Over range(尽量使用 enumerate 取代 range)](#item-7--prefer-enumerate-over-range尽量使用-enumerate-取代-range)
    - [1. 相比于`range()`，`enumerate()`的优势是？](#1-相比于rangeenumerate的优势是)
  - [Item 8: Use zip to Process Iterators in Parallel(用 zip()函数 同时遍历两个迭代器)](#item-8-use-zip-to-process-iterators-in-parallel用-zip函数-同时遍历两个迭代器)
    - [8.1 用zip() 迭代两个迭代器的优势是？](#81-用zip-迭代两个迭代器的优势是)
  - [Item 9: Avoid else Blocks After for and while Loops(不要在while和for循环后面写else块)](#item-9-avoid-else-blocks-after-for-and-while-loops不要在while和for循环后面写else块)
    - [1. 为什么要避免使用？](#1-为什么要避免使用)
  - [Item 10: Prevent Repetition with Assignment Expressions(用赋值表达式减少重复代码)](#item-10-prevent-repetition-with-assignment-expressions用赋值表达式减少重复代码)
    - [1. 赋值表达式好在哪？](#1-赋值表达式好在哪)
    - [2. 怎么用？](#2-怎么用)
- [二、Lists and Dictionaries](#二lists-and-dictionaries)
  - [Item 11: Know How to Slice Sequences(学会对序列切片)](#item-11-know-how-to-slice-sequences学会对序列切片)
    - [1. 什么样的类可以进行切片操作？](#1-什么样的类可以进行切片操作)
    - [2. 切片时应该秉承什么样的原则？](#2-切片时应该秉承什么样的原则)
      - [3. `a`和`b`都是列表，`a = b` 和 `a = b[:]` 有何区别？](#3-a和b都是列表a--b-和-a--b-有何区别)
  - [Item 12: Avoid Striding and Slicing in a Single Expression(不要在切片操作里同时指定 起止下标 和 步长)](#item-12-avoid-striding-and-slicing-in-a-single-expression不要在切片操作里同时指定-起止下标-和-步长)
    - [原因](#原因)
  - [Item 13: Prefer Catch-All Unpacking Over Slicing(尽量通过带星号的`unpacking`操作来捕获多个元素，而不是通过切片)](#item-13-prefer-catch-all-unpacking-over-slicing尽量通过带星号的unpacking操作来捕获多个元素而不是通过切片)
    - [1. 如何通过`unpacking`操作来完成 切片 的功能？](#1-如何通过unpacking操作来完成-切片-的功能)
    - [2. 相比于切片，`unpacking`操作捕获的优势在哪？](#2-相比于切片unpacking操作捕获的优势在哪)
  - [Item 14: Sort by Complex Criteria Using the key Parameter(用 `sort`方法的`key`参数 来表示复杂的排序逻辑)](#item-14-sort-by-complex-criteria-using-the-key-parameter用-sort方法的key参数-来表示复杂的排序逻辑)
    - [1. 为什么要用`key`参数指定排序逻辑？](#1-为什么要用key参数指定排序逻辑)
    - [2. 如何用多个条件来排序？](#2-如何用多个条件来排序)
      - [2.1 利用 元组 来实现](#21-利用-元组-来实现)
      - [2.2 多次调用`sort()`](#22-多次调用sort)
      - [2.3 如果多个条件中，我们希望一个条件排正序，一个排倒序，应该怎么做？](#23-如果多个条件中我们希望一个条件排正序一个排倒序应该怎么做)
  - [Item 15: Be Cautious When Relying on dict Insertion Ordering(不要过分依赖给字典添加条目时所用的顺序)](#item-15-be-cautious-when-relying-on-dict-insertion-ordering不要过分依赖给字典添加条目时所用的顺序)
    - [1. 字典的`key`是否有序？](#1-字典的key是否有序)
    - [2. 为什么不能总是假设所有的字典都能保留键值对插入时的顺序？](#2-为什么不能总是假设所有的字典都能保留键值对插入时的顺序)
      - [2.2 如何解决这个问题呢？](#22-如何解决这个问题呢)
  - [Item 16: Prefer get Over in and KeyError to Handle Missing Dictionary Keys(用`get`处理键不在字典里的情况，而不是`in`和`KeyError`)](#item-16-prefer-get-over-in-and-keyerror-to-handle-missing-dictionary-keys用get处理键不在字典里的情况而不是in和keyerror)
    - [1. 有哪些方法 可以处理 key不在字典里的情况？](#1-有哪些方法-可以处理-key不在字典里的情况)
    - [2. 更推荐哪种方法？为什么？](#2-更推荐哪种方法为什么)
      - [2.1 先说结论](#21-先说结论)
      - [2.2 几个例子](#22-几个例子)
        - [2.2.1 例子一：](#221-例子一)
        - [2.2.2 例子二：](#222-例子二)
  - [Item 17: Prefer defaultdict Over setdefault to Handle Missing Items in Internal State(用`defaultdict`处理缺失的元素，而不是`setdefault`)](#item-17-prefer-defaultdict-over-setdefault-to-handle-missing-items-in-internal-state用defaultdict处理缺失的元素而不是setdefault)
  - [Item 18: Know How to Construct Key-Dependent Default Values with __missing__(学会利用`__missing__`构造依赖建的默认值)](#item-18-know-how-to-construct-key-dependent-default-values-with-missing学会利用__missing__构造依赖建的默认值)
    - [(1) 用`defaultdict`来解决：](#1-用defaultdict来解决)
    - [(2) 重载内置`dict`的`__missing__`方法](#2-重载内置dict的__missing__方法)
- [三、函数](#三函数)
  - [Item 19: Never Unpack More Than Three Variables When Functions Return Multiple Values(不要把函数返回的多个数值 拆分到三个以上的变量中)](#item-19-never-unpack-more-than-three-variables-when-functions-return-multiple-values不要把函数返回的多个数值-拆分到三个以上的变量中)
    - [1. 为什么不要这么做？](#1-为什么不要这么做)
    - [2. 如果函数必须返回3个以上的变量，应该怎么做？](#2-如果函数必须返回3个以上的变量应该怎么做)
  - [Item 20: Prefer Raising Exceptions to Returning None(遇到意外时应该抛异常，而不是返回`None`)](#item-20-prefer-raising-exceptions-to-returning-none遇到意外时应该抛异常而不是返回none)
    - [1. 为什么不推荐返回`None`？](#1-为什么不推荐返回none)
    - [2. 既然不推荐返回`None`，那如何解决这个问题呢？](#2-既然不推荐返回none那如何解决这个问题呢)
      - [方法一：](#方法一)
      - [方法二：](#方法二)
  - [Item 21: Know How Closures Interact with Variable Scope(了解如何在闭包里使用外围作用域中的变量)](#item-21-know-how-closures-interact-with-variable-scope了解如何在闭包里使用外围作用域中的变量)
  - [Item 22: Reduce Visual Noise with Variable Positional Arguments(用数量可变的位置参数给函数设计清晰的参数列表)](#item-22-reduce-visual-noise-with-variable-positional-arguments用数量可变的位置参数给函数设计清晰的参数列表)
    - [1. 如何把一个已有序列传给参数可变的函数？](#1-如何把一个已有序列传给参数可变的函数)
    - [2. 接收数量可变参数的函数 可能存在什么问题？](#2-接收数量可变参数的函数-可能存在什么问题)
  - [Item 23: Provide Optional Behavior with Keyword Arguments(用关键字参数来表示可选的行为)](#item-23-provide-optional-behavior-with-keyword-arguments用关键字参数来表示可选的行为)
    - [1. 什么是 按位置传递参数 和 按关键字传递参数 来调用函数？](#1-什么是-按位置传递参数-和-按关键字传递参数-来调用函数)
    - [2. 通过关键字指定参数时，需要注意什么？](#2-通过关键字指定参数时需要注意什么)
    - [3. 如何通过字典来调用关键字参数？](#3-如何通过字典来调用关键字参数)
      - [3.2 字典里的关键字可以有 带调用函数不存在的关键字吗？](#32-字典里的关键字可以有-带调用函数不存在的关键字吗)
    - [4. 关键字参数有何好处？](#4-关键字参数有何好处)
  - [Item 24: Use None and Docstrings to Specify Dynamic Default Arg(用`None`和`docstring`来描述默认值会变的参数)](#item-24-use-none-and-docstrings-to-specify-dynamic-default-arg用none和docstring来描述默认值会变的参数)
    - [1. 如何给函数提供 变化的默认实参？](#1-如何给函数提供-变化的默认实参)
      - [1.1 陷阱](#11-陷阱)
      - [1.2 破解之法](#12-破解之法)
    - [2. 扩展](#2-扩展)
    - [总结](#总结)
  - [Item 25: Enforce Clarity with Keyword-Only and Positional-Only Arguments(用 只能以关键字指定 和只 能按位置传入 的参数 来设计清晰的参数列表)](#item-25-enforce-clarity-with-keyword-only-and-positional-only-arguments用-只能以关键字指定-和只-能按位置传入-的参数-来设计清晰的参数列表)
    - [1. 只能通过关键字指定的参数(keyword-only argument)](#1-只能通过关键字指定的参数keyword-only-argument)
      - [1.1 什么时候需要使用 keyword-only argument？](#11-什么时候需要使用-keyword-only-argument)
      - [1.2 如何使用 keyword-only argument？](#12-如何使用-keyword-only-argument)
    - [2. 只能按位置传递的参数(Positional-Only Arguments)](#2-只能按位置传递的参数positional-only-arguments)
      - [2.1 为什么需要？](#21-为什么需要)
      - [2.2 如何使用？](#22-如何使用)
    - [3. `*`和`/`同时出现在参数列表中时，它俩中间的参数必须按什么提供实参？](#3-和同时出现在参数列表中时它俩中间的参数必须按什么提供实参)
  - [Item 26: Define Function Decorators with functools.wraps(用`functools.wraps`定义函数装饰器)](#item-26-define-function-decorators-with-functoolswraps用functoolswraps定义函数装饰器)
- [四、 Comprehensions and Generators(推导与生成)](#四-comprehensions-and-generators推导与生成)
  - [Item 27: Use Comprehensions Instead of map and filter(用列表推导来替代`map`和`filter`)](#item-27-use-comprehensions-instead-of-map-and-filter用列表推导来替代map和filter)
  - [Item 28: Avoid More Than Two Control Subexpressions in Comprehensions(控制推导逻辑的子表达式不要超过两个)](#item-28-avoid-more-than-two-control-subexpressions-in-comprehensions控制推导逻辑的子表达式不要超过两个)
  - [Item 29: Avoid Repeated Work in Comprehensions by Using Assignment Expressions(用赋值表达式消除推导式中的重复代码)](#item-29-avoid-repeated-work-in-comprehensions-by-using-assignment-expressions用赋值表达式消除推导式中的重复代码)
  - [Item 30: Consider Generators Instead of Returning Lists(不要让函数直接返回列表，而应该让它逐个生成列表里的值)](#item-30-consider-generators-instead-of-returning-lists不要让函数直接返回列表而应该让它逐个生成列表里的值)
  - [Item 31: Be Defensive When Iterating Over Arguments(谨慎的迭代函数所收到的参数)](#item-31-be-defensive-when-iterating-over-arguments谨慎的迭代函数所收到的参数)
    - [1. 当函数收到什么类型的参数时需要谨慎的迭代？为什么？](#1-当函数收到什么类型的参数时需要谨慎的迭代为什么)
    - [2. 如何解决上面的问题？](#2-如何解决上面的问题)
    - [3. 既然函数收到的实参为迭代器的时候可能会遇到问题，那如何避免这种情况呢？](#3-既然函数收到的实参为迭代器的时候可能会遇到问题那如何避免这种情况呢)
    - [4. 总结](#4-总结)
  - [Item 32: Consider Generator Expressions for Large List Comprehensions(对于数据量较大的列表推导，尽量用生成器表达式来完成)](#item-32-consider-generator-expressions-for-large-list-comprehensions对于数据量较大的列表推导尽量用生成器表达式来完成)
    - [1. 为什么？](#1-为什么)
    - [2. 生成器表达式 如何写？](#2-生成器表达式-如何写)
    - [3. 使用生成器表达式需要注意什么？](#3-使用生成器表达式需要注意什么)
  - [Item 33: Compose Multiple Generators with yield from(通过`yiled from`把多个生成器连起来)](#item-33-compose-multiple-generators-with-yield-from通过yiled-from把多个生成器连起来)
    - [1. 为什么建议使用`yiled from`？](#1-为什么建议使用yiled-from)
  - [Item 34: Avoid Injecting Data into Generators with send(不要用`send`给生成器注入数据)](#item-34-avoid-injecting-data-into-generators-with-send不要用send给生成器注入数据)
    - [1. 为什么不建议使用`send`给生成器注入数据？](#1-为什么不建议使用send给生成器注入数据)
    - [2. 既然不推荐使用`send()`向生成器发送数据，那如何和生成器交互？](#2-既然不推荐使用send向生成器发送数据那如何和生成器交互)
  - [Item 35: Avoid Causing State Transitions in Generators with throw(不要通过`throw`变换生成器的状态)](#item-35-avoid-causing-state-transitions-in-generators-with-throw不要通过throw变换生成器的状态)
  - [Item 36: Consider itertools for Working with Iterators and Generators(考虑用`itertools`来拼装迭代器和生成器)](#item-36-consider-itertools-for-working-with-iterators-and-generators考虑用itertools来拼装迭代器和生成器)
    - [1. `itertools`模块介绍](#1-itertools模块介绍)
    - [2. 函数介绍](#2-函数介绍)
- [五、类与接口](#五类与接口)
  - [Item 37: Compose Classes Instead of Nesting Many Levels of Built-in Types(不要用嵌套的内置类型实现多层结构，而应该通过组合起来的类来实现)](#item-37-compose-classes-instead-of-nesting-many-levels-of-built-in-types不要用嵌套的内置类型实现多层结构而应该通过组合起来的类来实现)
    - [1. 用 嵌套的内置类型 实现多层结构 的缺点是？](#1-用-嵌套的内置类型-实现多层结构-的缺点是)
    - [2. 如何解决上面的困境呢？](#2-如何解决上面的困境呢)
  - [Item 38: Accept Functions Instead of Classes for Simple Interfaces(让简单的接口接受函数，而不是类的实例)](#item-38-accept-functions-instead-of-classes-for-simple-interfaces让简单的接口接受函数而不是类的实例)
  - [Item 39: Use @classmethod Polymorphism to Construct Objects Generically(通过`@classmethod`多态来构造同一体系中的各类对象)](#item-39-use-classmethod-polymorphism-to-construct-objects-generically通过classmethod多态来构造同一体系中的各类对象)
    - [1.](#1)
  - [Item 40: Initialize Parent Classes with super(通过`super()`来初始化父类)](#item-40-initialize-parent-classes-with-super通过super来初始化父类)
    - [1. 有哪几种初始化父类的方法？](#1-有哪几种初始化父类的方法)
    - [2. 上面的几种初始化父类的方法中，更推荐哪种方法？为什么？](#2-上面的几种初始化父类的方法中更推荐哪种方法为什么)
  - [Item 41: Consider Composing Functionality with Mix-in Classes(考虑使用`mixin`类来表示可组合的功能)](#item-41-consider-composing-functionality-with-mix-in-classes考虑使用mixin类来表示可组合的功能)
    - [1. 什么时候应该使用`mixin`类？](#1-什么时候应该使用mixin类)
  - [Item 42: Prefer Public Attributes Over Private Ones(优先考虑用public属性表示应受保护的数据，而不是用`private`)](#item-42-prefer-public-attributes-over-private-ones优先考虑用public属性表示应受保护的数据而不是用private)
    - [1. 为什么应该避免使用`private`？](#1-为什么应该避免使用private)
    - [2. 对于那些不希望被别人访问的成员，我们应该怎么做？](#2-对于那些不希望被别人访问的成员我们应该怎么做)
    - [3. 那什么时候适合用`private`？](#3-那什么时候适合用private)
  - [Item 43: Inherit from collections.abc for Custom Container Types(自定义的容器类型应该从`collections.abc`继承)](#item-43-inherit-from-collectionsabc-for-custom-container-types自定义的容器类型应该从collectionsabc继承)
    - [1. `collections.abc`提供的是什么？](#1-collectionsabc提供的是什么)
    - [2. 假设一个类，我们想让他支持`dict`那样的关键字索引操作(如`obj[key]`)，应该怎么做？](#2-假设一个类我们想让他支持dict那样的关键字索引操作如objkey应该怎么做)
    - [3. 为什么更推荐直接继承`collections.abc`中的抽象类，而不是自己实现呢？](#3-为什么更推荐直接继承collectionsabc中的抽象类而不是自己实现呢)
    - [4. 定义一个二叉树类，让它支持内置`list`一样的操作](#4-定义一个二叉树类让它支持内置list一样的操作)
- [六、元类与属性](#六元类与属性)
  - [Item 44: Use Plain Attributes Instead of Setter and Getter Methods(用纯属性与修饰器取代旧式的`setter`和`getter`方法)](#item-44-use-plain-attributes-instead-of-setter-and-getter-methods用纯属性与修饰器取代旧式的setter和getter方法)
  - [Item 45: Consider @property Instead of Refactoring Attributes(考虑用`@property`实现新的属性访问逻辑，不要着急重构代码)](#item-45-consider-property-instead-of-refactoring-attributes考虑用property实现新的属性访问逻辑不要着急重构代码)
    - [1. 课文中讲了啥？](#1-课文中讲了啥)
    - [2. 作者想通过上面的例子表达什么？](#2-作者想通过上面的例子表达什么)
    - [3. 既然`@property`可以做到在不影响现有代码的情况下，对数据模型进行重构，那是不是只要一致用`@property`就行了？](#3-既然property可以做到在不影响现有代码的情况下对数据模型进行重构那是不是只要一致用property就行了)
  - [Item 46: Use Descriptors for Reusable @property Methods(用描述符来改写需要复用的`@property`方法)](#item-46-use-descriptors-for-reusable-property-methods用描述符来改写需要复用的property方法)
    - [1. 内置的`@property`有何缺点？](#1-内置的property有何缺点)
    - [2. 如何解决这个缺点？](#2-如何解决这个缺点)
      - [2.1 版本一：](#21-版本一)
      - [2.2 版本二：](#22-版本二)
      - [2.3 版本三：](#23-版本三)
  - [Item 47: Use __getattr__, __getattribute__, and __setattr__ for Lazy Attributes(针对懒惰属性使用`__getattr__, __getattribute__, __setattr__`)](#item-47-use-getattr-getattribute-and-setattr-for-lazy-attributes针对懒惰属性使用__getattr__-__getattribute__-__setattr__)
  - [Item 48 - Item 51](#item-48---item-51)
- [第七章、Concurrency and Parallelism(并发与并行)](#第七章concurrency-and-parallelism并发与并行)
  - [Item 52: Use subprocess to Manage Child Processes(用`subprocess`管理子进程)](#item-52-use-subprocess-to-manage-child-processes用subprocess管理子进程)
- [第八章、稳定与性能](#第八章稳定与性能)
  - [Item 65: Take Advantage of Each Block in `try/except/else/finally`(合理利用`try/except/else/finally`结构中的每个代码块)](#item-65-take-advantage-of-each-block-in-tryexceptelsefinally合理利用tryexceptelsefinally结构中的每个代码块)
  - [Item 66: Consider contextlib and with Statements for Reusable try/finally Behavior（考虑用`contextlib`和`with`语句来改写可复用的`try/finally`）](#item-66-consider-contextlib-and-with-statements-for-reusable-tryfinally-behavior考虑用contextlib和with语句来改写可复用的tryfinally)
    - [1. 这一节主要介绍了什么？](#1-这一节主要介绍了什么)
    - [2. 使用 上下文管理器 来实现可复用的`try/finally`](#2-使用-上下文管理器-来实现可复用的tryfinally)
  - [Item 67: Use datetime Instead of time for Local Clocks（用`datetime`处理本地时间，而不是`time`）](#item-67-use-datetime-instead-of-time-for-local-clocks用datetime处理本地时间而不是time)
  - [Item 68: Make pickle Reliable with copyreg （使用`copyreg`实现可靠的`pickle`操作）](#item-68-make-pickle-reliable-with-copyreg-使用copyreg实现可靠的pickle操作)
    - [1. `pickle`操作存在什么问题？](#1-pickle操作存在什么问题)
    - [2. 如何解决上面的问题？](#2-如何解决上面的问题-1)
  - [Item 69: Use decimal When Precision Is Paramount(在需要准确计算的场合，用`decimal`表示相应的数值)](#item-69-use-decimal-when-precision-is-paramount在需要准确计算的场合用decimal表示相应的数值)
    - [1. python的浮点数存在什么问题？](#1-python的浮点数存在什么问题)
    - [2. 为什么python的浮点数会有这样的问题？](#2-为什么python的浮点数会有这样的问题)
    - [3. 如何解决这个问题？](#3-如何解决这个问题)
    - [4. 使用`Decimal`类时要注意什么？](#4-使用decimal类时要注意什么)
    - [5. 对小数进行四舍五入](#5-对小数进行四舍五入)
      - [5.1 `round()`函数](#51-round函数)
      - [5.2 自定义小数的舍入规则](#52-自定义小数的舍入规则)
  - [Item 70: Profile Before Optimizing(先分析性能，然后再进行优化)](#item-70-profile-before-optimizing先分析性能然后再进行优化)
    - [1. 为什么需要分析性能再进行优化？](#1-为什么需要分析性能再进行优化)
    - [2. 利用工具分析代码性能](#2-利用工具分析代码性能)
      - [2.1 在Python中，一般用什么工具分析代码性能？更推荐使用哪个？](#21-在python中一般用什么工具分析代码性能更推荐使用哪个)
      - [2.2 分析程序的性能时，需要注意什么？](#22-分析程序的性能时需要注意什么)
      - [2.3 如何使用`cProfile`进行性能分析？](#23-如何使用cprofile进行性能分析)
  - [Item 71: Prefer deque for Producer–Consumer Queues(优先考虑使用`deque`实现 生产者-消费者队列(即`FIFO`))](#item-71-prefer-deque-for-producerconsumer-queues优先考虑使用deque实现-生产者-消费者队列即fifo)
    - [1. 为什么`deque`比`list`更适合实现`FIFO`？](#1-为什么deque比list更适合实现fifo)
  - [Item 72: Consider Searching Sorted Sequences with bisect(考虑用`bisect`搜索已排序的序列)](#item-72-consider-searching-sorted-sequences-with-bisect考虑用bisect搜索已排序的序列)
  - [Item 73: Know How to Use heapq for Priority Queues(使用`heap`制作优先级队列)](#item-73-know-how-to-use-heapq-for-priority-queues使用heap制作优先级队列)
    - [1. 优先级队列(priority queue)是什么？](#1-优先级队列priority-queue是什么)
    - [2. 如何使用 优先级队列？](#2-如何使用-优先级队列)
  - [Item 74: Consider memoryview and bytearray for Zero-Copy Interactions with bytes(考虑使用 `memoryview`和`bytearray` 来实现 零拷贝的`bytes`操作)](#item-74-consider-memoryview-and-bytearray-for-zero-copy-interactions-with-bytes考虑使用-memoryview和bytearray-来实现-零拷贝的bytes操作)
    - [1. 缓冲协议(buffer protocol)](#1-缓冲协议buffer-protocol)
      - [.1 缓冲协议 的作用是？](#1-缓冲协议-的作用是)
      - [2.2 哪些对象支持缓冲协议？](#22-哪些对象支持缓冲协议)
    - [2. 内存视图`memoryview`](#2-内存视图memoryview)
      - [2.1 `memoryview`的作用是？](#21-memoryview的作用是)
      - [2.2 如何使用 `memoryview`？](#22-如何使用-memoryview)
        - [(1) 构造函数](#1-构造函数)
        - [(2) 类属性](#2-类属性)
        - [(3) 类方法](#3-类方法)
        - [(4) 使用实例](#4-使用实例)
      - [2.3 `memoryview`一般在什么场景下使用？](#23-memoryview一般在什么场景下使用)
      - [2.4 `memoryview`构建的内存视图 和 原对象是什么关系？](#24-memoryview构建的内存视图-和-原对象是什么关系)
      - [2.5 `memoryview`对象能否修改？](#25-memoryview对象能否修改)
    - [3. `bytearray`](#3-bytearray)
      - [3.1 `bytearray` 和 `bytes`有何异同？](#31-bytearray-和-bytes有何异同)
      - [3.2 什么场景需要使用 `bytearray` ？](#32-什么场景需要使用-bytearray-)
    - [4. 使用`memoryview`和`bytearray`来构建流媒体服务器](#4-使用memoryview和bytearray来构建流媒体服务器)
      - [4.1 发送数据](#41-发送数据)
      - [4.2 接收数据](#42-接收数据)
      - [4.3 实例](#43-实例)
    - [参考文献](#参考文献)
- [第九章 测试与调试](#第九章-测试与调试)
  - [tem 75: Use repr Strings for Debugging Output(通过`repr`字符串输出调试信息)](#tem-75-use-repr-strings-for-debugging-output通过repr字符串输出调试信息)
    - [1. `__str__`和`__repr__`的作用](#1-__str__和__repr__的作用)
- [参考文献](#参考文献-1)


&emsp;
&emsp; 
# 一、Pythonic Thinking
## Item 1：Know Which Version of Python You’re Using
### 1. 为什么要确定python的版本？
因为在命令行中，`python`可能意味着`python2`（比如centOS7中）：
```shell
$ python --version
```
输出为
```
Python 2.7.5
```

### 2. 有哪些方法可以获取python的版本？
**(1) 命令行获取**
```shell
$ python3 --version
```
输出为
```
Python 3.7.0a1
```
**(2) 在程序运行时获取**
```python
import sys
print(sys.version_info)
```
运行结果:
```
sys.version_info(major=3, minor=7, micro=0, releaselevel='alpha', serial=1)
3.7.0a1 (default, Jan 12 2019, 22:18:25) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-16)]
```






&emsp;
&emsp;
&emsp;
## Item 2：Follow the PEP 8 Style Guide
### 1. 什么是 PEP 8？






&emsp;
&emsp;
&emsp;
## Item 3：Know the Differences Between bytes and str
### 1. 如何定义`bytes`和`str`？
`bytes`一般是形如`b'Hello'`,`str`前面没有`b`
```python
bytes_tmp = b'h\x65llo'     # bytes
str_tmp = 'a\u0300 propos'  # str
```

### 2. `python`的`bytes`和`str` 分别表示的是什么？
#### 2.1 `bytes`
&emsp;&emsp; `bytes` 包含的是 **原始数据**(8位无符号值，一般用`ASCII`编码)，只负责**以字节序列的形式（二进制形式）来存储数据**，至于这些数据到底表示什么内容（字符串、数字、图片、音频等），完全由程序的解析方式决定。如果采用合适的字符编码方式（字符集），字节串可以恢复成字符串；反之亦然，字符串也可以转换成字节串。
&emsp;&emsp; 说白了，`bytes` 只是简单地记录内存中的原始数据，至于如何使用这些数据，`bytes` 并不在意，你想怎么使用就怎么使用，`bytes` 并不约束你的行为。

#### 2.2 `str`
&emsp;&emsp; 前面已经提到，`bytes`里存的是原始数据，它的存在形式是`01010001110`这种。我们无论是在写代码，还是阅读文章的过程中，肯定不会有人直接阅读这种比特流，它必须有一个编码方式，使得它变成有意义的比特流，而不是一堆晦涩难懂的`01`组合。
&emsp;&emsp; 和`bytes`相对的是，`str` 中包含的是表示人类语言文本字符的`Unicode`数据，这样人们看的时候就方便了。

### 3.  `bytes`和`str`之间如何转换？
> 要将`Unicode`数据转换为二进制数据，必须调用str的`encode`方法。
> 要将二进制数据转换为`Unicode`，必须调用bytes的 `decode` 方法
> 
其实可以这么理解：
> `str`保存的是人能看懂的数据，因此`str`到`bytes`的转换就是 加密(encode)；
> `bytes`保存的是人看不懂的二进制数据，因此`bytes`到`str`的转换就是 解密(decode)。
> 
书中有这样一句话是：
> &emsp;&emsp; Importantly, str instances do not have an associated binary encoding, and bytes instances do not have an associated text encoding.（str实例没有相关联的二进制编码，而bytes实例也没有相关联的文本编码）
> 
这句话的想表达意思是，`str`和`byte`之间没有绑定关系：
> `str`可以加密(encode)为`gbk`编码的二进制序列，也可以加密(encode)为`utf8`编码的二进制序列，加密(encoding)为`gb2312`等其它编码当然也没问题；
> `bytes`可以解码(decode)为`gbk`编码的二进制序列，也可以解码(decode)为`bytes`编码的二进制序列。
> 
我们来验证一下：
```python
>>> a = "python教程"
>>> a.encode("gbk")
# b'python\xbd\xcc\xb3\xcc'
>>> a.encode() # 使用默认编码，即utf8
# b'python\xe6\x95\x99\xe7\xa8\x8b'
>>> b = a.encode()
>>> b.decode()
'python教程'
>>> b
# b'python\xe6\x95\x99\xe7\xa8\x8b'
```
从上面的结果我们可以看到，同样是 `"python教程"`，用`gbk`和`utf8`得到的`bytes`是不一样的，也验证了 `str`和`byte`之间没有绑定关系 这一结论。

### 4. 对于`bytes`数据，为什么 `print()`和`list()`出来的数据不一样呢？
```python
>>> b = b'Hello'
>>> print(b)
# b'Hello'
>>> b
# b'Hello'
>>> print(b.__str__())
# b'Hello'
>>> b[0]
# 72
>>> print(list(b))
# [72, 101, 108, 108, 111]
```
因为`print(obj)`调用的是`obi.__str__()`来输出的，`bytes`类型的`__str__`方法返回的就是这个形式；

### 5. `bytes`和`str`的兼容性
`bytes`和`str`不兼容，它们不能混用：
**(1) 拼接**
通过使用`+`运算符，可以`bytes + bytes` ，`str + str`，但是不能 `bytes + str`
```python
>>> b'hello' + b'world'
# b'helloworld'
>>> 'hello' + 'world'
# 'helloworld'
>>> b'hello' + 'world' 
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't concat str to bytes
```
**(2) 比较**
不能对`bytes`和`str`进行`>`、`<`的比较
```python
>>> assert "hello" > "Hello"

>>> assert "hello" > b"Hello"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '>' not supported between instances of 'str' and 'bytes'
```
对`bytes`和`str`进行`==`，结果总会是返回`False`，即使里面包含了一样的数据。
```python
>>> print(b'foo' == 'foo')
#False
```
**(3) 用`%`格式化字符**
不能用`str`来格式化一个`bytes`字符串；
但可以用`bytes`来格式化一个`str`字符串，因为调用的是`__repr__`来替换`%s`的：
```python
>>> print(b"Hello %s" % b"world")
# b'Hello world'

>>> print("Hello %s" % b"world")
# Hello b'world'

>>> print(b"Hello %s" % "world")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: %b requires a bytes-like object, or an object that implements __bytes__, not 'str'
```

### 6. 读写文件
#### 6.1 写文件需要遵守的方针
**以什么模式打开的，就只能写该类型的字符串：**
> 以文本模式，也就是`Unicode`打开的文件只能写`str`;
> 以二进制打开的文件只能写`bytes`
> 
#### 6.2 写文件 
&emsp;&emsp; 值得注意的是，由`open()`默认以Unicode打开文件。
```python
>>> with open("test.txt", "w") as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 

Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
TypeError: write() argument must be str, not bytes
```
因为`mode('w')`是以 默认模式(文本模式) 打开，而文本模式期望的输入是`str`， 写入的却是二进制数据`b'\xf1\xf2\xf3\xf4\xf5'`， 所以报错。
再来看下面的代码
```python
>>> with open("test.txt", "wb") as f:
...     f.write("World")
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
TypeError: a bytes-like object is required, not 'str'
```
上面的代码用 `mode('wb')`(二进制模式)打开文件，写入的却是`str`类型，所以也报错
正确的做法如下：
```python
>>> with open("test.txt", "wb") as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 
5

>>> with open("test.txt", "w") as f:
...     f.write("World")
... 
5
```
以什么模式打开就写入什么类型的数据，不要混用。

#### 6.3 读文件
类似的问题，在`read()`的时候也存在：
```python
>>> with open('data.bin', 'wb') as f:
...     f.write(b'\xf1\xf2\xf3\xf4\xf5')
... 
5

>>> with open('data.bin', 'r') as f:
...     data = f.read()
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "/usr/local/bin/python3/lib/python3.7/codecs.py", line 322, in decode
    (result, consumed) = self._buffer_decode(data, self.errors, final)
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 0: invalid continuation byte
```
**错误的原因：**
&emsp;&emsp; 因为文件是以 文本模式(`r`)打开的，当文件是以文本模式打开时，它使用系统默认的文件编码(即`utf8`)来翻译二进制文件：
> bytes.encode (for writing) and str.decode (for reading)
> 写文件用`bytes.encode()`，读文件用`str.decode()`
> 
在上面的代码中，前面写入了`b'\xf1\xf2\xf3\xf4\xf5'`，因此后面读的时候就相当于 `str.decode(b'\xf1\xf2\xf3\xf4\xf5')`，因此报错为
```
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 0: invalid continuation byte
```
**但是，反过来就不会报错：**
```python
>>> with open('data.bin', 'w') as f:
...     f.write("Hello World")
... 
#11

>>> with open('data.bin', 'rb') as f:
...     data = f.read()
... 
>>> data
#b'Hello World'
```
**为什么反过来不会报错？**
&emsp;&emsp; 因为文件都是以二进制形式保存的，以 二进制(`rb`)的方式打开当然不会有问题。

### 7. 总结
&emsp;&emsp; ① py3中，有两中类型可以表示字符串：`bytes`和`str`，`bytes`中保存的是8位无符号数，`str`中保存的是`unicode`编码的文本类型；
&emsp;&emsp; ② 不能将`bytes`和`str`用操作符(如`>, ==, +,  %)`进行混合操作；
&emsp;&emsp; ③ 如果想往文件里 读/写 二进制数据，则应该使用 二进制模式打开文件(`rb` 或 `wb`)
&emsp;&emsp; ④ 如果您想在文件中读取或写入Unicode数据，请注意系统的默认文本编码.如果希望避免意外，则显式地指定编码方式传递给`open()`，如 `open('tmp.txt', 'gbk'`、`open('tmp.txt', 'utf8'`)，不能依赖系统默认的编码方式。






&emsp;
&emsp;
&emsp;
## Item 4：Prefer Interpolated F-Strings Over C-style Format Strings and str.format
### 1. Python有哪些格式化字符串的方法？
一共有四种：
> ① 使用 `%` 来格式化 C风格字符串
> ② 使用`dic`来格式化 `%` 来格式化 C风格字符串
> ③ 使用内置的`str`类的`format()`方法
> ④ 使用插值(interpolated format string，简称f-string)

####  1.1 方法一：使用 `%` 来格式化 C风格字符串
##### 1.1.1 优点
简单

##### 1.1.2 缺点：
**问题1：一旦顺序或类型错了，会在运行时报错：**
```python
value = 1.234
formatted_str = '%-10s = %.2f' % (key, value)
print(formatted_str)
>>> 
# my_var     = 1.23

formatted_str = '%-10s = %.2f' % (value, key)
>>> 
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: must be real number, not str
```
为了避免上述错误，我们必须保证类型和顺序的正确性。

**问题2：当要对 准备填进去的值 做处理时，表达式会变得很冗长**
```python
pantry = [
	('avocados', 1.25),
	('bananas', 2.5),
	('cherries', 15),
]
for i, (item, count) in enumerate(pantry):
	print('#%d: %-10s = %.2f' % (i, item, count))
```
运行结果为：
```
#0: avocados   = 1.25
#1: bananas    = 2.50
#2: cherries   = 15.00
```
如果想让输出的数据更好理解，就需要做一点小小的改动：
```python
for i, (item, count) in enumerate(pantry):
	print('#%d: %-10s = %d' % (
		i + 1,
		item.title(), 
		round(count)))
```
运行结果为：
```
#1: Avocados   = 1
#2: Bananas    = 2
#3: Cherries   = 15
```
可以看到的是，代码在修改过之后变得很冗长，可读性变差了。

**问题3：如果想用同一个值来填充格式字符里的多个位置，那就必须在`%`后面的元组中多次重复该值**
```python
template = '%s loves food. See %s cook.'
name = 'Max'
formatted = template % (name, name) # name变量需要写两次
print(formatted)
```
可以看到的是，`name`变量需要写两次，而且如果需要修改`name`，则需要改两次，这很麻烦，而且还很容易漏。

**问题4：**

### 1.2 方法二：使用`dic`来格式化 `%` 来格式化 C风格字符串
#### 1.2.1 优点
用`dic`来替代元组，这可以解决**问题1**和**问题3**：
解决问题1：
```python
key = 'my_var'
value = 1.23414  

old_way = '%-10s = %.2f' % (key, value)

new_way = '%(key)-10s = %(value).2f' % {
	'key': key, 'value': value} # Original
reordered = '%(key)-10s = %(value).2f' % {
	'value': value, 'key': key} # 调换了位置也不影响
assert old_way == new_way == reordered
```
解决问题3：
```python
name = 'Max'

template = '%s loves food. See %s cook.'
before = template % (name, name) # %右侧为元组

template = '%(name)s loves food. See %(name)s cook.'
after = template % {'name': name} # %右侧为字典

assert before == after
```

#### 1.2.2 缺点
用`dic`来替代元组 让 **问题2** 变得更严重了：
```python
for i, (item, count) in enumerate(pantry):
	before = '#%d: %-10s = %d' % (
		i + 1,
		item.title(),
		round(count))

	after = '#%(loop)d: %(item)-10s = %(count)d' % {
		'loop': i + 1,
		'item': item.title(),
		'count': round(count),
		}

	assert before == after	
```
我们可以看到的是，用`dic`来替代元组后，代码变得更为冗长了，可读性也随之下降。

### 1.3 方法三：使用内置的`str`类的`format()`方法
`str.formant()`

### 1.4 方法四：使用 插值格式字符串(interpolated format string，简称f-string)
&emsp;&emsp; 插值格式字符串 是在`Python3.6`中引入的特性，可以完美解决前面提到的问题。
#### 1.4.1 怎么使用 插值格式字符串？
① 在格式字符的前面加上`f`，比如`f'{key} : {value}'`；
② 可以直接在`f-string`的`{}`里面直接引用当前作用域可见的变量；
③ 还能直接进行函数调用：`{round(count)}`；
④ 支持`str.format`那套迷你语言(也就是在`{}`内的冒号右侧采用的那套规则)：`{item.title():^20s}`
```python
panpantry = [
    ('avocados', 1.25),
    ('bananas', 2.5),
    ('cherries', 15),
]

for i, (item, count) in enumerate(pantry):
    print(f"#{i+1}: {item.title():^20s} = {round(count)}") 
```

#### 1.4.2 `str.format()`方法 和 `f-string`的联系
&emsp;&emsp; `f-string`支持`str.fformat()`所支持的 那套迷你语言，也就是在`{}`内的冒号右侧采用的那套规则也可以用到`f-sting`里面，而且也可以通过`!`把值转换成`Unicode`及`repr`形式的字符串。

#### 1.4.3 插值格式字符串 好在哪？
&emsp;&emsp; **可以直接在 格式字符串内 直接引用 变量**，这个特性彻底解决了前面四个问题，既简洁明了，又不存在顺序弄错的问题，下，下面的代码对四种方法进行了对比，结论一目了然：
```python
pantry = [
    ('avocados', 1.25),
    ('bananas', 2.5),
    ('cherries', 15),
]

for i, (item, count) in enumerate(pantry):
	c_style_tuple = '#%d: %-10s = %d' % (
		i + 1,
		item.title(),
		round(count))
	print(c_style_tuple)

	c_style_dic = '#%(loop)d: %(item)-10s = %(count)d' % {
		'loop': i + 1,
		'item': item.title(),
		'count': round(count),
	}
	print(c_style_dic)

	str_format = '#{}: {:<10s} = {}'.format(
		i + 1,
		item.title(),
		round(count))
	print(str_format)

	# f-string 一行代码搞定
    print(f"#{i+1}: {item.title():^20s} = {round(count)}") 
```






&emsp;
&emsp;
&emsp;
## Item 5：Write Helper Functions Instead of Complex Expressions(用辅助函数取代复杂的表达式)
### 1. 这条规则指的是？
&emsp;&emsp; 对于那些复杂的表达式，尤其是会重复利用的那种复杂表达式，应该定义一个辅助函数来完成。

### 2. 为什么？
① 可读性强，更容易被他人理解；
② 遵循`DRY`原则(Do't Repeat Yourself)，能复用的代码都应该封装成一个函数，可避免代码冗长。






&emsp;
&emsp; 
&emsp; 
## Item 6: Prefer Multiple Assignment Unpacking Over Indexing(把数据直接解包到多个变量里，不要通过下标范围)
### 1. 为什么不建议使用下标访问？
使用下标访问会降低程序的可读性，还能减少代码量：
```python
snacks = [('bacon', 350), ('donut', 240), ('muffin', 190)]

# 用下标访问
for i in range(len(snacks)):
    item = snacks[i]
    name = item[0]
    calories = item[1]
    print(f'#{i+1}: {name} has {calories} calories')

print("*"*30)

# 解包到多个变量中
for rank, (name, calories) in enumerate(snacks, 1):
    print(f'#{rank} : {name.title()} has {calories} calories.')
```
运行结果：
```
#1: bacon has 350 calories
#2: donut has 240 calories
#3: muffin has 190 calories
******************************
#1 : Bacon has 350 calories.
#2 : Donut has 240 calories.
#3 : Muffin has 190 calories.
```
显然，用`unpacking`和`enumerate`函数代码量减少了很多，可行性也大大的提高了。





&emsp;
&emsp;
&emsp;
## Item 7:  Prefer enumerate Over range(尽量使用 enumerate 取代 range)
### 1. 相比于`range()`，`enumerate()`的优势是？
有时在迭代`list`时，需要获取当前处理的元素在`list`中的位置，`enumerate()`会简洁一些：
```python
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']

for i in range(len(flavor_list)):
	flavor = flavor_list[i]
	print(f'{i + 1}: {flavor}')

# 使用 enumerate()
for i, flavor in enumerate(flavor_list, 1):
	print(f'{i}: {flavor}')
```
这又回到了`item 6`，避免使用下标访问容器。





&emsp;
&emsp;
&emsp;
## Item 8: Use zip to Process Iterators in Parallel(用 zip()函数 同时遍历两个迭代器)
### 8.1 用zip() 迭代两个迭代器的优势是？
更为简洁，换句话说就是更`pythonic`，来看一段代码对比：
```python
names = ['Cecilia', 'Lise', 'Marie']
counts = [len(n) for n in names]

longest_name = None
max_count = 0

# 用range()
for i in range(len(names)):
	count = counts[i]
	if count > max_count:
		longest_name = names[i]
		max_count = count

# 用 enumerate()
for i, name in enumerate(names):
	count = counts[i]
	if count > max_count:
		longest_name = name
		max_count = count		

# 用 zip() + unpacking机制
# zip(names, counts)负责将元素封装成元组，然后在用 unpacking机制将元组里的值赋给  name和count
for name, count in zip(names, counts):
	if count > max_count:
		longest_name = name
		max_count = count		
```






&emsp;
&emsp;
&emsp;
## Item 9: Avoid else Blocks After for and while Loops(不要在while和for循环后面写else块)
### 1. 为什么要避免使用？
&emsp;&emsp; 因为 `for … else`、`while … else`，`else` 和 `if/else`的语法不一样，这很容易让人产生误解。






&emsp;
&emsp;
&emsp;
## Item 10: Prevent Repetition with Assignment Expressions(用赋值表达式减少重复代码)
### 1. 赋值表达式好在哪？
① 节省代码
② 可读性好

### 2. 怎么用？
见其它位置的笔记






&emsp;
&emsp;
&emsp;
# 二、Lists and Dictionaries 
## Item 11: Know How to Slice Sequences(学会对序列切片)
### 1. 什么样的类可以进行切片操作？
&emsp;&emsp; 实现了 `__getitem__ `and `__setitem__` 的类都可以进行切片操作。

### 2. 切片时应该秉承什么样的原则？
&emsp;&emsp; 切片要尽可能写的简答，如果是从头开始切割，则应该省略左侧的下标`0`；如果是一直取到末尾，那就应该省略冒号右侧的下标
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

from_start = numbers[:4]
print(from_start)

print(to_end:=numbers[1:])
```
输出的结果：
```
[0, 1, 2, 3]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### 3. `a`和`b`都是列表，`a = b` 和 `a = b[:]` 有何区别？
&emsp;&emsp; 切片`a = b[:]` 是 浅拷贝， `a = b`是直接赋值，所以它们之间的区别就是 直接赋值 和 浅拷贝的关系。来写段代码验证一下：
```python
a = [0, 1, 2, ['a', 'b', 'c'], 4]
b = a[:]

if a == b :
    print("a == b")

a[0] = 'A' 		# 修改列表
a[3][0] = 100	# 修改子序列

print("a = ", a)
print("b = ", b)
```
运行结果：
```
a == b
a =  ['A', 1, 2, [100, 'b', 'c'], 4]
b =  [0, 1, 2, [100, 'b', 'c'], 4]
```
**结果分析：**
&emsp;&emsp; 运行结果显示，列表`a`的直接修改没有影响到列表`b`，但是对列表`a`的子序列的修改却影响到了`b`，这显然验证了 切片 是浅拷贝的结论。






&emsp;
&emsp;
&emsp;
## Item 12: Avoid Striding and Slicing in a Single Expression(不要在切片操作里同时指定 起止下标 和 步长)
### 原因
&emsp;&emsp; 可读性不好，其他人很难理解






&emsp;
&emsp;
&emsp;
## Item 13: Prefer Catch-All Unpacking Over Slicing(尽量通过带星号的`unpacking`操作来捕获多个元素，而不是通过切片)
### 1. 如何通过`unpacking`操作来完成 切片 的功能？
&emsp;&emsp; 搭配 带星号表达式(starred expressiong) 即可：
```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_descending = sorted(car_ages, reverse=True)
oldest, second_oldest , *others = car_ages_descending
```

### 2. 相比于切片，`unpacking`操作捕获的优势在哪？
> ① 简短易读；
> ② 用切片对序列进行切分的时候更容易出错
> 
就拿取序列开头两个元素来举例吧：
```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_descending = sorted(car_ages, reverse=True)

# 解包
oldest, second_oldest , *others = car_ages_descending

print(oldest, second_oldest, others)

# 切片
oldest = car_ages_descending[0]
second_oldest = car_ages_descending[1]
others = car_ages_descending[2:]

print(oldest, second_oldest, others)
```
从上面的代码可知，解包操作不但代码少，而且读起来也更通畅；而且切片操作还得注意下标，很容易出错。






&emsp;
&emsp;
&emsp;
## Item 14: Sort by Complex Criteria Using the key Parameter(用 `sort`方法的`key`参数 来表示复杂的排序逻辑)
### 1. 为什么要用`key`参数指定排序逻辑？
对于一些自定义的类，无法用`sort()`进行排序，此时可通过`key`参数来指定排序规则。
```python
class Tool:
	def __init__(self, name, weight):
		self.name = name
		self.weight = weight
	def __repr__(self):
		return f'Tool({self.name!r}, {self.weight})'

tools = [
	Tool('level', 3.5),
	Tool('hammer', 1.25),
	Tool('screwdriver', 0.5),
	Tool('chisel', 0.25),
]

tools.sort()
```
运行结果：
```
Traceback (most recent call last):
  File "test.py", line 15, in <module>
    tools.sort()
TypeError: '<' not supported between instances of 'Tool' and 'Tool'
```
将上面的代码修改如下：
```python
tools.sort(key=lambda x : x.name)
print(tools)
```
运行结果如下：
```
[Tool('chisel', 0.25), Tool('hammer', 1.25), Tool('level', 3.5), Tool('screwdriver', 0.5)]
```

### 2. 如何用多个条件来排序？
#### 2.1 利用 元组 来实现
&emsp;&emsp; 我们都知道，两个元组在比较的时候，如果首元素相等，那就比较第二个元素，如果第二个也相等，那就继续往下比较，利用元组的这个特性，我们可以实现多条件排序。
&emsp;&emsp; 比如我们希望`tools`先以`weight`来排序，在`weight`相等的情况下再以`name`排序。我们可以构造一个元组`(weight, name)`：
```python
tools.sort(key=lambda x : (x.weight, x.name))
print(tools)
```
运行结果：
```
[Tool('chisel', 0.25), Tool('screwdriver', 0.5), Tool('hammer', 1.25), Tool('level', 3.5)]
```
**但是利用元组来实现有一个不足之处：** 如果多个指标中，我们希望一个指标排正序，一个排倒序，元组就不能实现了。

#### 2.2 多次调用`sort()`
&emsp;&emsp; 因为`sort()`是稳定排序(准确的说是`timsort`，一种改进过的归并排序)，这意味着如果`key`函数认为两个值相等，那么这两个值在排序结果中的先后顺序会与它们在排序前的顺序一致，因此多次调用`sort()`不会有问题。

#### 2.3 如果多个条件中，我们希望一个条件排正序，一个排倒序，应该怎么做？
**① 用元组**
&emsp;&emsp; 如果这多个条件中有一个是数字，可以通过对为数字的条件取倒数来完成
**② 多次调用`sort()`，对需要逆序的那个用`reverse=True`参数来指定逆序。**






&emsp;
&emsp;
&emsp;
## Item 15: Be Cautious When Relying on dict Insertion Ordering(不要过分依赖给字典添加条目时所用的顺序)
### 1. 字典的`key`是否有序？
&emsp; 在`Python3.7`开始，我们可以确信迭代 标准的字典(注意是标准的字典) 时
> &emsp;&emsp; 在`Python 3.5`之前 的版本中，`dict`所提供的许多方法（包括`keys`、`values`、i`tems`与`popitem`等）都不保证固定的顺序。
> &emsp;&emsp; 从`Python 3.6`开始，字典会保留这些 键值对 **在添加时所用的顺序**，而且`Python3.7`版的语言规范正式确立了这条规则。
> 

### 2. 为什么不能总是假设所有的字典都能保留键值对插入时的顺序？
&emsp;&emsp; 因为在`python`中，我们很容易就拿自定义出 和标准`dict`很像，但本身不是字典的类，而对于这些类型的对象，我们不能假设 迭代时看到的顺序 会和 插入的顺序一致。
#### 2.2 如何解决这个问题呢？
① 重新定义函数，让这个函数不依赖插入时的顺序；

② 在函数的开头先判断传进来的对象是不是 标准的`dict`对象：
```python
def get_winner(ranks):
	if not isinstance(ranks, dict):
		raise TypeError('must provide a dict instance')
	return next(iter(ranks))
```

③ 通过 注解(type annotation) 来保证传过来的是 标准的`dict`对象
&emsp;&emsp; 






&emsp;
&emsp;
&emsp;
## Item 16: Prefer get Over in and KeyError to Handle Missing Dictionary Keys(用`get`处理键不在字典里的情况，而不是`in`和`KeyError`)
### 1. 有哪些方法 可以处理 key不在字典里的情况？
① 用`if/else`和`in`表达式；
② 利用 `KeyError`异常；
③ 用`dict.get(key, default_value)`来完成
&emsp;&emsp; 当对`dict`进行`get()`时，若`key`不在将返回`None`，我们可以对其设一个默认值来处理`key`不在的情况。
④ `setdefaultt(key, default=None)`方法
&emsp;&emsp; 内置的`dict`类型 提供了`setdefault`方法，这个方法会先查询字典里是否有这个key，如果有就返回对应的值，没有的话就给它一个默认值(默认是`None`)。

### 2. 更推荐哪种方法？为什么？
#### 2.1 先说结论
&emsp;&emsp; 如果和`key`相关联的值是像 计数器 这样的基本类型，那么`get`将是最好的方案；
&emsp;&emsp; 如果是那种构造起来开销大，或是容器出错的类型，那么可以把`get`与赋值表达式结合起来用：
```python
if (names := votes.get(key)) is None:
	votes[key] = names = []
names.append(who)
```

#### 2.2 几个例子
##### 2.2.1 例子一：
&emsp;&emsp; 假如我们要给一家三明治设计菜单，想先确定大家喜欢吃哪些类型的面包，于是我们定义了这样的一个字典：`{ 面包类型 : 得票数 }`
```python
counters = {
	'pumpernickel': 2,
	'sourdough': 1,
}

# ① 用`if/else`和`in`表达式
key = 'wheat'
if key in counters:
	count = counters[key]
else:
	count = 0
counters[key] = count + 1

# ② 利用 `KeyError`异常；
try:
	count = counters[key]
except KeyError:
	count = 0
counters[key] = count + 1

# ③ 用`dict.get(key, default_value)`来完成
count = counters.get(key, 0)
counters[key] = count + 1
```
从上面可以看到，当`value`为计数类型时，用`get`方法是最简单易懂的。

##### 2.2.2 例子二：
&emsp;&emsp; 如果需求变了，这次不仅要记录得票数，还要记录投票的人，因此我们需要用一个列表关联起来：
```python
votes = {
'baguette': ['Bob', 'Alice'],
'ciabatta': ['Coco', 'Deb'],
}
key = 'brioche'
who = 'Elmer'

```
**① 用`if/else`和`in`表达式；**
```python
if key in votes:
	names = votes[key]
else:
	votes[key] = names = []
names.append(who)
print(votes)
```

**② 利用 `KeyError`异常；**
```python
try:
	names = votes[key]
except KeyError:
	votes[key] = names = []
names.append(who)
```
**③ 用`dict.get(key, default_value)`来完成**
```python
if (names := votes.get(key)) is None:
	votes[key] = names = []
names.append(who)
```
**④ `setdefault`方法**
```python
names = votes.setdefault(key, [])
names.append(who)
```
**结论：**
&emsp;&emsp; `setdefault`方法用的代码最少，但是这个写法不太好理解，因为该方法的名字 `setdefault`很难让人立即明白它的作用，因此还是更推荐`get()`和赋值表达式的写法。






&emsp;
&emsp;
&emsp;
## Item 17: Prefer defaultdict Over setdefault to Handle Missing Items in Internal State(用`defaultdict`处理缺失的元素，而不是`setdefault`)
&emsp;&emsp; `Item 17`提到的四种方法适合用在字典不是自己创建的情况，如果字典是自己创建的，那么 内置模块`collections` 提供的 `defaultdict`类 可以轻松解决问题。
```python
# --coding:utf8--
from collections import defaultdict

class Visitors:
    def __init__(self):
        self.data = defaultdict(set)
    
    def add(self, country, city):
        self.data[country].add(city)


v = Visitors()
v.add('England', "London")
v.add("America", "New York")
v.add("China", "Beijing")
v.add("China", "Shanghai")

print(v.data)
```
运行结果：
```
defaultdict(<class 'set'>, {'England': {'London'}, 'America': {'New York'}, 'China': {'Shanghai', 'Beijing'}})
```
上面的`add`方法相当简短，而且可以确保访问不存在的键时总会得到一个`set`类型的实例。






&emsp;
&emsp;
&emsp;
## Item 18: Know How to Construct Key-Dependent Default Values with __missing__(学会利用`__missing__`构造依赖建的默认值)
&emsp;&emsp; Item 17 介绍的`defaultdict`是一个解决`key`不在字典里的好方法，但是有些情况是`defaultdict`处理不了的：
> &emsp;&emsp; 假设我们要写一个程序，在文件系统里管理社交网络账号中的图片。这个程序应该用字典把这些图片的路径名跟相关的文件句柄关联起来，这样我们就能方便地读取并写入图像了。
> 
### (1) 用`defaultdict`来解决：
```python
from collections import defaultdict

def open_picture(profile_path):
    try:
        return open(profile_path, 'a+b')
    except OSError:
        print(f'Failed to open path {profile_path}')
    raise

path = "c:\test.png"
pictures = defaultdict(open_picture)
handle = pictures[path]
handle.seek(0)
image_data = handle.read()
```
运行结果：
```
Traceback (most recent call last):
  File "d:/code_practice/practice.py", line 12, in <module>
    handle = pictures[path]
TypeError: open_picture() missing 1 required positional argument: 'profile_path'
```
**结果分析：**
&emsp;&emsp; 程序出错的原因在于，传给`defaultdict`的只能是一个不需要参数的函数，而我们写的辅助函数`open_picture()`必须接受一个参数来`open`对应的图片，因此`defaultdict`无法完成任务。

### (2) 重载内置`dict`的`__missing__`方法
```python
def open_picture(profile_path):
    try:
        print("int try")
        return open(profile_path, 'a+b')
    except OSError:
        print(f'Failed to open path {profile_path}')
        raise


class Pictures(dict): # 注意，重载的是内置的dict
    def __missing__(self, key):
        value = open_picture(key)
        self[key] = value
        return value

path = "C\practice"
pictures = Pictures()
handle = pictures[path]
handle.seek(0)
image_data = handle.read()
print(image_data)
```
**分析:**
&emsp;&emsp; 在访问`pictures[path]`时，如果`pictures`里没有这个键，那就会调用`__missing__`方法。这个方法必须根据key参数创建一份新的默认值，系统会把这个默认值插入字典并返回给调用放。以后再访问pictures[path]，就不会调用__missing__了，因为字典里已经有了对应的键与值。






&emsp;
&emsp;
&emsp;
# 三、函数
## Item 19: Never Unpack More Than Three Variables When Functions Return Multiple Values(不要把函数返回的多个数值 拆分到三个以上的变量中)
### 1. 为什么不要这么做？
有两个原因：
&emsp;&emsp; ① 返回的变量多了，如果对其进行拆分，很容易弄错顺序，而且这种bug很难排查；
&emsp;&emsp; ② 对多个变量进行解包时，一行代码很容易变得很长，这和PEP8风格指南相悖，需要拆分成多行，这让代码看起来很别扭。

### 2. 如果函数必须返回3个以上的变量，应该怎么做？
&emsp;&emsp; 可以将这些变量放在一个`namedtuple`中，然后将这个`namedtuple`返回。






&emsp;
&emsp;
&emsp;
## Item 20: Prefer Raising Exceptions to Returning None(遇到意外时应该抛异常，而不是返回`None`)
### 1. 为什么不推荐返回`None`？
&emsp;&emsp; 一般返回`None`来表示异常情况，但是这么做很容易出错，因为在 条件表达式中 无法区分`None`和`0`(或空`str`)，因为这些值都相当于`False`:
```python
def careful_divide(a, b):
	try:
		return a / b
	except ZeroDivisionError:
		return None

ret = careful_divide(0, 1)

if ret is None:
	print("Invalid input")
else:
	print(ret)

print("-"*20)

if not ret:
	print("Invalid input")
else:
	print(ret)
```
运行结果：
```
0.0
--------------------
Invalid input
```
**结果分析：**
&emsp;&emsp; 除非你明确的判断返回值是否为`None`，要不然在`if`语句中很容易将`0`和空字符串当成`False`，这样就不能将其和`None`区分开来了。

### 2. 既然不推荐返回`None`，那如何解决这个问题呢？
#### 方法一：
&emsp;&emsp; 返回两个值：`flag`表示是否成功，`value`表示计算结果
#### 方法二：
&emsp;&emsp; 不采用`None`作为特例，而是向调用方抛出异常，并在docstring中说明会抛什么异常，最后调用方负责捕获异常：
```python
def careful_divide(a: float, b: float) -> float:
	"""Divides a by b.
	Raises:
	ValueError: When the inputs cannot be divided.
	"""
	try:
		return a / b
	except ZeroDivisionError as e:
		raise ValueError('Invalid inputs')


def call_careful_divide(x, y):
	try:
		result = careful_divide(x, y)
	except ValueError:
		print('Invalid inputs')
	else:
		print('Result is %.1f' % result)


call_careful_divide(2, 5) 
call_careful_divide(1, 0)
```
运行结果：
```
Result is 0.4
Invalid inputs
```
上面的代码就很清晰，调用方出错的概率就变得很小了。






&emsp;
&emsp;
&emsp;
## Item 21: Know How Closures Interact with Variable Scope(了解如何在闭包里使用外围作用域中的变量)
&emsp;&emsp; 尽量少用`nonlocal`语句，尤其是在那种很长的函数中，因为有bug很难被发现。






&emsp;
&emsp;
&emsp;
## Item 22: Reduce Visual Noise with Variable Positional Arguments(用数量可变的位置参数给函数设计清晰的参数列表)

### 1. 如何把一个已有序列传给参数可变的函数？
&emsp;&emsp; 在传递序列的时候采用`*`即可：
```python
def log(message, *values): # The only difference
    if not values:
        print(message)
    else:
        values_str = ', '.join(str(x) for x in values)
        print(f'{message}: {values_str}')

favorites = [7, 33, 99]

log('Favorite numebers', *favorites)
```
运行结果：
```
Favorite numebers: 7, 33, 99
```

### 2. 接收数量可变参数的函数 可能存在什么问题？
有可能导致两个问题：
**① 如果`*`操作符加在生成器前，那么传递参数时，程序有可能因为内存耗尽而奔溃**
&emsp;&emsp; 程序总是必须先把 这些可变参数 转化成一个元组，然后才能把它们当成可选的位置参数传给函数。这意味着，如果调用函数时，把带`*`操作符的生成器传了过去，那么程序必须先把这个生成器里面的所有元素迭代完以便形成元组，然后才能继续继续往下执行。这个元组包含生成器所给出的每个值，这可能会耗费大量内存，甚至会让程序奔溃：
```python
def my_generator(num):
    for i in range(num):
        yield num

def my_varargs(*varargs):
    print(f"type  : {type(varargs)} \nvalue : {varargs}")


a = my_generator(10)
my_varargs(*a)
```
运行结果：
```
type  : <class 'tuple'> 
value : (10, 10, 10, 10, 10, 10, 10, 10, 10, 10)
```
**② 可能导致很难排查的bug**






&emsp;
&emsp;
&emsp;
## Item 23: Provide Optional Behavior with Keyword Arguments(用关键字参数来表示可选的行为)
### 1. 什么是 按位置传递参数 和 按关键字传递参数 来调用函数？
```python
def remainder(number, divisor):
	return number % divisor

# 按位置传递参数
remainder(20, 7) 

# 按关键字传递参数
remainder(20, divisor=7)
remainder(number=20, divisor=7)
```

### 2. 通过关键字指定参数时，需要注意什么？
**① 如果混用 按位置传递参数 和 按关键字传递参数，则必须将 位置参数 放在前面**
```python
def remainder(number, divisor):
	return number % divisor

remainder(number=20, 7)
```
运行结果：
```
  File "f:\code\python\test\test.py", line 3
    remainder(number=20, 7)
                          ^
SyntaxError: positional argument follows keyword argument
```
**② 每个参数只能指定一次，不能既使用位置参数指定，又使用关键字参数指定**
```python
def remainder(number, divisor):
	return number % divisor

remainder(20, number=7)
```
运行结果：
```
Traceback (most recent call last):
  File "f:\code\python\test\test.py", line 4, in <module>
    remainder(20, number=7)
TypeError: remainder() got multiple values for argument 'number'
```

### 3. 如何通过字典来调用关键字参数？
把`**`加在字典前面，传给函数：
```python
def remainder(number, divisor):
	return number % divisor
	
my_kwargs = {
    'number': 20,
    'divisor': 7,
}
print(remainder(**my_kwargs))
```
#### 3.2 字典里的关键字可以有 带调用函数不存在的关键字吗？
不可以：
```python
def remainder(number, divisor):
	return number % divisor
	
my_kwargs = {
    'number': 20,
    'divisor': 7,
    'useless': 1,
}
print(remainder(**my_kwargs))
```
运行结果：
```
Traceback (most recent call last):
  File "f:\code\python\test\test.py", line 9, in <module>
    print(remainder(**my_kwargs))
TypeError: remainder() got an unexpected keyword argument 'useless'
```

### 4. 关键字参数有何好处？
**① 用关键字参数调用函数可以让初次阅读代码的人更容易看懂。**
**② 函数可以带有默认值，通过这个默认值我们可以定义可选行为：**
&emsp;&emsp; 例如，我们要计算液体流入容器的速率。如果这个容器带刻度，那么可以取前后两个时间点的刻度差，并把它跟这两个时间点的时间差相除，就可以算出流速了。
```python
def flow_rate(weight_diff, time_diff):
	return weight_diff / time_diff

weight_diff = 0.5
time_diff = 3
flow = flow_rate(weight_diff, time_diff)
print(f'{flow:.3} kg per second')
#  0.167 kg per second
```
一般来说，我们用每秒的千克数表示流速。但有时，我们还想估算更长的时间段内的流速结果。只需要给同一个函数加一个`period`参数来表示那个时间段相当于多少秒即可:
```python
def flow_rate(weight_diff, time_diff, period):
	return (weight_diff / time_diff) * period
```
这样写有个问题，就是每次调用函数时，都得明确指定`period`参数，哪怕计算每秒中的流速，也可以指定`period`为1。
```python
flow_per_second = flow_rate(weight_diff, time_diff, 1)
```
为了简化这种用法，我们可以给`period`参数设定默认值：
```python
def flow_rate(weight_diff, time_diff, period=1):
	return (weight_diff / time_diff) * period
```
这样`period`就变成可选参数了：
```python
flow_per_second = flow_rate(weight_diff, time_diff)
flow_per_hour = flow_rate(weight_diff, time_diff, period=3600)
```
**③ 我们可以很灵活地扩充函数的参数，而不用担心会影响原有的函数调用代码。**






&emsp;
&emsp;
&emsp;
## Item 24: Use None and Docstrings to Specify Dynamic Default Arg(用`None`和`docstring`来描述默认值会变的参数)
### 1. 如何给函数提供 变化的默认实参？
#### 1.1 陷阱
&emsp;&emsp; 有时，我们想把那种不能提前固定的值，当做关键字参数的默认值。例如，记录日志消息时，默认时间应该是触发事件的那一刻。所以，如果调用者没有明确指定时间，那么默认把调用函数的那一刻当成这条日志的记录时间。
```python
from time import sleep
from datetime import datetime

def log(message, when=datetime.now()):
    print(f'{when}: {message}')

log('Hi there!')
sleep(0.1)
log('Hello again!')
```
运行结果：
```
2021-11-09 14:20:51.545934: Hi there!
2021-11-09 14:20:51.545934: Hello again!
```
**结果分析：**
&emsp;&emsp; 根据运行结果可发现，两次`log()`记录的时间一模一样，这是因为`datetime.now()`只在加载该模块的时候执行了一次，后续的调用的都是这次的运行结果。也就是说，函数参数的默认值在程序运行时就确定了。
#### 1.2 破解之法
&emsp;&emsp; 要想在`Python`中实现 这种效果，惯用的办法是把参数的默认值设为`None`，同时在`docstring`文档里写清楚：
> 这个参数为`None`时，函数会如何运作，而且在函数里要判断该参数是不是`None`，如果是则把它改为默认值。
> 
```python
def log(message, when=None):
    """Log a message with a timestamp.
    Args:
        message: Message to print.
        when: datetime of when the message occurred.
            Defaults to the present time.
    """
    if when is None:
        when = datetime.now()
    print(f'{when}: {message}')


log('Hi there!')
sleep(0.1)
log('Hello again!')
```
运行结果：
```
2021-11-09 15:00:54.230603: Hi there!
2021-11-09 15:00:54.337931: Hello again!
```
**结果分析：**
&emsp;&emsp; 现在可以看到，两次运行结果的时间戳不一样了。

### 2. 扩展
&emsp;&emsp; 把参数的默认值写成`None`还有个重要的意义：就是用来表示那种以后可能由调用者修改内容的默认值。
&emsp;&emsp; 我们要写一个函数对采用JSON格式编码的数据做解码。如果无法解码，那么就返回调用时所指定的默认结果，假如调用者当时没有明确指定，那么久返回一个空`dict`:
```python
import json

def decode(data, default={}):
	try:
		return json.loads(data)
	except ValueError:
		return default

foo = decode('bad data')
foo['stuff'] = 5
bar = decode('also bad')
bar['meep'] = 1

print('Foo:', foo)
print('Bar:', bar)		
```
运行结果：
```
Foo: {'stuff': 5, 'meep': 1}
Bar: {'stuff': 5, 'meep': 1}
```
**结果分析：**
&emsp;&emsp; 我们本意是想让这两次操作得到两个不同的空白字典。但实际上，它们用的是同一个字典，只要修改其中一个字典，另一个就会受到影响。
**这种错误的根源在于：**
&emsp;&emsp; `foo`和`bar`其实是同一个字典，都是一开始给`default`的那个确认默认值时所分配的空字典：
```python
# 前面如上，略 ...

if foo is bar:
	print("foo is bar")
else:
	print("foo is not bar")
```
运行结果：
```
foo is bar
```
**要解决上面的问题，依然是把默认值设为`None`，并且在`docstring`中说明函数在这个值为`None`时会怎么做：**
```python
import json

def decode(data, default=None):
	"""Load JSON data from a string.
	Args:
	data: JSON data to decode.
	default: Value to return if decoding fails.
	Defaults to an empty dictionary.
	"""
	try:
		return json.loads(data)
	except ValueError:
		if default is None:
			default = {}
		return default

foo = decode('bad data')
foo['stuff'] = 5
bar = decode('also bad')
bar['meep'] = 1
print('Foo:', foo)
print('Bar:', bar)
if foo is  bar:
	print("foo is bar")
else:
	print("foo is not bar")
```
运行结果：
```
Foo: {'stuff': 5}
Bar: {'meep': 1}
foo is not bar
```
**结果分析：**
&emsp;&emsp; 可以看到的是，`foo`和`bar`相互独立了。

### 总结
&emsp;&emsp; 函数的默认实参只会在 **系统把定义该函数的模块加载进来的时候** 计算一次，因此如果默认值将来可能由调用方修改(例如`dict`和`list`等) 或 要随着调用时的情况变化时，那么程序可能会出现奇怪的效果。






&emsp;
&emsp;
&emsp;
## Item 25: Enforce Clarity with Keyword-Only and Positional-Only Arguments(用 只能以关键字指定 和只 能按位置传入 的参数 来设计清晰的参数列表)
### 1. 只能通过关键字指定的参数(keyword-only argument)
#### 1.1 什么时候需要使用 keyword-only argument？
&emsp;&emsp; 一般来说，对于那些比较容易混淆的参数，都建议使用，举个例子：
```python
def safe_division(number, divisor, ignore_overflow=False, ignore_zero_division=False):
	try:
		return number / divisor
	except OverflowError:
		if ignore_overflow:
			return 0
		else:
			raise
	except ZeroDivisionError:
		if ignore_zero_division:
			return float('inf')
		else:
			raise


print(safe_division_b(1.0, 10**500, ignore_overflow=True))
print(safe_division_b(1.0, 0, ignore_zero_division=True))
```
运行结果：
```
0
inf
```
但是，`safe_division()`的`ignore_overflow`和`ignore_zero_division`参数都是可选的，我们没有办法要求调用者必须使用关键字形式来指定这两个参数，比如：
```python
safe_division(1.0, 10**500, True, False)
```
如果像上面那样调用的话代码就不够清晰了，很容易搞错，这个时候，keyword-only argument 就排上用处了。

#### 1.2 如何使用 keyword-only argument？
&emsp;&emsp; 在参数列表中使用`*`将参数分成两组，左边是位置参数，右边是 只能用关键字指定的参数：
```python
def safe_division(number, divisor, *, ignore_overflow=False, ignore_zero_division=False):
	try:
		return number / divisor
	except OverflowError:
		if ignore_overflow:
			return 0
		else:
			raise
	except ZeroDivisionError:
		if ignore_zero_division:
			return float('inf')
		else:
			raise


print(safe_division(1.0, 10**500, True))
```
运行结果：
```
Traceback (most recent call last):
  File "d:\code_practice\practice.py", line 16, in <module>
    print(safe_division(1.0, 10**500, True))
TypeError: safe_division() takes 2 positional arguments but 3 were given
```
将调用代码改成：
```python
print(safe_division(1.0, 10**500, ignore_overflow=True))
print(safe_division(1.0, 0, ignore_zero_division=True))
```
程序正常运行

### 2. 只能按位置传递的参数(Positional-Only Arguments)
#### 2.1 为什么需要？
按关键字传递参数会存在一个问题：如果我们对函数进行了重构，把函数的形参名修改了，如果在代码中有对这些形参名进行按关键字传递，那么代码可能会报错：
```python
def safe_division(a, divisor, *, ignore_overflow=False, ignore_zero_division=False):
	# 略...

print(safe_division(number=1.0, 10**500, True))
```
显然，我们在修改`safe_division()`后，后面对它的调用是错误的。

#### 2.2 如何使用？
&emsp;&emsp; python3.8 引入了 只能按位置传递的参数(Positional-Only Arguments)：在参数列表中使用`/`符号，表示它左边的那些参数必须按位置指定
```python
def safe_division(number, divisor, /, *, ignore_overflow=False, ignore_zero_division=False):
	try:
		return number / divisor
	except OverflowError:
		if ignore_overflow:
			return 0
		else:
			raise
	except ZeroDivisionError:
		if ignore_zero_division:
			return float('inf')
		else:
			raise


print(safe_division(number=1.0, 10**500, ignore_overflow=True))
```
运行结果：
```
  File "d:\code_practice\practice.py", line 16
    print(safe_division(number=1.0, 10**500, ignore_overflow=True))
                                                                 ^
SyntaxError: positional argument follows keyword argument
```

### 3. `*`和`/`同时出现在参数列表中时，它俩中间的参数必须按什么提供实参？
&emsp;&emsp; 在`*`和`/` 中间的参数 既可以按关键字提供参数，也可以按位置提供参数，这也是python默认的参数指定方式。






&emsp;
&emsp;
&emsp;
## Item 26: Define Function Decorators with functools.wraps(用`functools.wraps`定义函数装饰器)







&emsp;
&emsp;
&emsp;
# 四、 Comprehensions and Generators(推导与生成)
## Item 27: Use Comprehensions Instead of map and filter(用列表推导来替代`map`和`filter`)







&emsp;
&emsp;
&emsp;
## Item 28: Avoid More Than Two Control Subexpressions in Comprehensions(控制推导逻辑的子表达式不要超过两个)
&emsp;&emsp; 用推导式本来就是为了使代码更简洁易懂，如果在推导式里面嵌套了太多层就起不到这个效果了。






&emsp;
&emsp;
&emsp;
## Item 29: Avoid Repeated Work in Comprehensions by Using Assignment Expressions(用赋值表达式消除推导式中的重复代码)
```python
stock = {
	'nails': 125,
	'screws': 35,
	'wingnuts': 8,
	'washers': 24,
}

order = ['screws', 'wingnuts', 'clips']

def get_batches(count, size):
	return count // size

# ① 使用for循环
result1 = {}
for name in order:
	count = stock.get(name, 0)
	batches = get_batches(count, 8)
	if batches:
		result1[name] = batches	
print("result1 : ", result1)


# ② 使用字典推导
result2 = {name : get_batches(stock.get(name, 0), 8) for name in order if get_batches(stock.get(name, 0), 8)}
print("result2 : ", result2)

# ③ 字典推导 + 赋值表达式
result3 = {name : tenth for name in order if (tenth:=get_batches(stock.get(name, 0), 8))}
print("result3 : ", result3)
```
很显然，用字典推导比用`for`循环简洁，而赋值表达式又更加简洁，而且还减少了一次对`get_batches()`的调用，提高了效率。






&emsp;
&emsp;
&emsp;
## Item 30: Consider Generators Instead of Returning Lists(不要让函数直接返回列表，而应该让它逐个生成列表里的值)
对于那些要返回一个列表的函数，利用`yield`来实现比较合适：
> ① 代码更清晰；
> ② 对于那些大列表，没有内存溢出的风险
> 






&emsp;
&emsp;
&emsp;
## Item 31: Be Defensive When Iterating Over Arguments(谨慎的迭代函数所收到的参数)
### 1. 当函数收到什么类型的参数时需要谨慎的迭代？为什么？
&emsp;&emsp; 收到 迭代器 的时候，而函数会对该迭代器进行多次遍历时，需要谨慎，因为迭代器只能进行一次遍历。
&emsp;&emsp; 假设我们要分析省内各市的游客数量，原始数据保存在一个列表中，列表每个元素标书每年有多少游客到这个城市旅游（单位为万），我们需要统计每个市占的百分比：
```python
def normalize(numbers):
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

visits = [15, 35, 80]
percentages = normalize(visits)
print(percentages)
assert sum(percentages) == 100.0
```
运行结果：
```
[11.538461538461538, 26.923076923076923, 61.53846153846154]
```
但为了应对更大的数据，我们现在需要从文件中读数据，文件中每一行包含一个市的数据。因为数据量可能变得很大，因此我们决定使用生成器来实现，以避免占用过多内存：
```python
def normalize(numbers):
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

it = read_visits("practice.txt")
percentages = normalize(it)
print(percentages)
```
运行结果：
```
[]
```
**结果分析：**
&emsp;&emsp; 奇怪的是，对`read_visits()`返回的迭代器调用`normalize()`后没有得到预期的结果，出现这个问题的原因是：**假如迭代器(或生成器)已经抛出`StopIteration`，继续用它来构造列表或做`for`循环是不会得到任何结果的。**不信我们来验证一下：
```python
def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

it = read_visits("practice.txt")
print("sum(it) ：", sum(it))
print("list(it): ", list(it))
```
运行结果：
```
sum(it) ： 401
list(it):  []
```
**而且还有一个很让人疑惑的是：为什么对 已经迭代完毕的迭代器 继续迭代 不会报错？** 因为包括`for`循环、`list`构造器和一些标准库函数都认为迭代器在正常的操作中抛出`StopIteration`异常是很正常的行为，因为它们没办法区分这个迭代器是本来就没有数据，还是本来有数据但是已经被迭代完了。

### 2. 如何解决上面的问题？
为了解决上面的问题，我们有三种方法：
> ① 我们可以在`normalize()`中将内容拷贝到一个列表中：
> ② 让`normalize()`接受一个函数，每次都用这个函数获取一个新的迭代器；
> ③ 新建一个容器类，这个容器类需要实现 迭代器协议
>  
**① 我们可以在`normalize()`中将内容拷贝到一个列表中：**
```python
def normalize_copy(numbers):
	numbers_copy = list(numbers) # Copy the iterator
	# 后面同上，略...
```
但问题是，我们用生成器的本意就是为了不占用过多内存，这样将内容拷贝到一个列表中，还不如一开始就给`normalize()`传一个列表。

**② 让`normalize()`接受一个函数，每次都用这个函数获取一个新的迭代器**

```python
def normalize(get_iter):
    total = sum(get_iter())
    result = []
    for value in get_iter():
        percent = 100 * value / total
        result.append(percent)
    return result

def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

percentages = normalize(lambda data_path="practice.txt": read_visits(data_path))
print(percentages)
assert sum(percentages) == 100.0
```
运行结果：
```
[24.93765586034913, 2.9925187032418954, 19.45137157107232, 24.688279301745634, 8.728179551122194, 7.4812967581047385, 5.985037406483791, 5.7356608478802995]
```
**结果分析：**
&emsp;&emsp; 这么做确实可以，但每次都给`normalize()`传一个`lambda`表达式显得有些生硬，不够优雅。

**③ 新建一个容器类，这个容器类需要实现 迭代器协议**
&emsp;&emsp; 所谓实现 迭代器协议(iterator protocol)，其实就是实现`__iter__()`方法，因为对一个对象进行迭代(如`for`循环)时，其实就是先通过`__iter__()`方法返回一个迭代器，然后再对这个返回的迭代器进行操作，具体原理可以看关于迭代器的笔记。
&emsp;&emsp; 听起来似乎很复杂，其实就是在实现`__iter__()`方法的时候返回一个生成器就行了：
```python
class ReadVisits:
    def __init__(self, data_path):
        self.data_path = data_path
    
    def __iter__(self):
        with open(self.data_path) as f:
            for line in f:
                yield int(line)

def normalize(numbers):
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result


percentages = normalize(ReadVisits("practice.txt"))
print(percentages)
assert sum(percentages) == 100.0
```
运行结果：
```
[24.93765586034913, 2.9925187032418954, 19.45137157107232, 24.688279301745634, 8.728179551122194, 7.4812967581047385, 5.985037406483791, 5.7356608478802995]  
```
**结果分析：**
&emsp;&emsp; 从结果可以看出，这么写是没问题的，但是为什么可以成功呢？因为`sum()`和`for`循环都会触发`ReadVisits.__iter__()`，然后得到一个新的迭代器。

### 3. 既然函数收到的实参为迭代器的时候可能会遇到问题，那如何避免这种情况呢？
&emsp;&emsp; 我们无法限定使用者传过来的参数，但是我们可以在函数中对接收到的实参进行判断，如果接受到的是会引发问题的类型，则返回错误，比如：
```python
from collections.abc import Iterator

class ReadVisits:
    def __init__(self, data_path):
        self.data_path = data_path
    
    def __iter__(self):
        with open(self.data_path) as f:
            for line in f:
                yield int(line)

def normalize(numbers):
    if isinstance(numbers, Iterator):
        raise TypeError("The parameter cannot be Iterator.")
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result


print(normalize(ReadVisits("practice.txt")))
print(normalize(iter(ReadVisits("practice.txt")))) # 传一个迭代器进去
```
运行结果：
```
[24.93765586034913, 2.9925187032418954, 19.45137157107232, 24.688279301745634, 8.728179551122194, 7.4812967581047385, 5.985037406483791, 5.7356608478802995]
Traceback (most recent call last):
  File "d:\code_practice\practice.py", line 24, in <module>
    print(normalize(iter(ReadVisits("practice.txt"))))
  File "d:\code_practice\practice.py", line 14, in normalize
    raise TypeError("The parameter cannot be Iterator.")
TypeError: The parameter cannot be Iterator.
```

### 4. 总结
&emsp;&emsp; 当函数要对接收到的实参遍历多次时需要格外注意，因为如果为迭代器，那么程序可能得不到预期的结果。






&emsp;
&emsp;
&emsp;
## Item 32: Consider Generator Expressions for Large List Comprehensions(对于数据量较大的列表推导，尽量用生成器表达式来完成)
### 1. 为什么？
&emsp;&emsp; 列表推导生成的元素全都会加载到内存里，如果数据量很大的话，有可能挤爆内存。

### 2. 生成器表达式 如何写？
和列表推导类似，把`[]`换成`()`即可：
```python
List_Comprehension = [x for x in range(10)]
Generator = (x for x in range(10))

print(List_Comprehension)
print(Generator)
```
运行结果：
```
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
<generator object <genexpr> at 0x000001FC7D659A10>
```

### 3. 使用生成器表达式需要注意什么？
&emsp;&emsp; 和迭代器一样，生成器表达式也只能迭代一次，因此如果代码需要对其进行多次迭代，那么就需要多注意，具体可以看Item 31。






&emsp;
&emsp;
&emsp;
## Item 33: Compose Multiple Generators with yield from(通过`yiled from`把多个生成器连起来)
### 1. 为什么建议使用`yiled from`？
有两个原因：
&emsp;&emsp; ① 使代码更简洁；
&emsp;&emsp; ② 解释器对`yiled from`进行了优化，速度要比用`for`循环迭代要快。






&emsp;
&emsp;
&emsp;
## Item 34: Avoid Injecting Data into Generators with send(不要用`send`给生成器注入数据)
### 1. 为什么不建议使用`send`给生成器注入数据？
主要有两个原因：
> ① 代码可读性差，别人很难理解；
> ② `send()`和`yield from`一起使用时会有奇怪的效果；
> 

### 2. 既然不推荐使用`send()`向生成器发送数据，那如何和生成器交互？
&emsp;&emsp; 可以通过迭代器向组合起来的生成器输入数据，具体见书上。TODO:






&emsp;
&emsp;
&emsp;
## Item 35: Avoid Causing State Transitions in Generators with throw(不要通过`throw`变换生成器的状态)
&emsp;&emsp; 因为不好懂TODO:






&emsp;
&emsp;
&emsp;
## Item 36: Consider itertools for Working with Iterators and Generators(考虑用`itertools`来拼装迭代器和生成器)
### 1. `itertools`模块介绍
&emsp;&emsp; `itertools`模块有很多函数，可以用来同时处理不同的迭代器。

### 2. 函数介绍
这个太多了，直接翻书吧。







&emsp;
&emsp;
&emsp;
# 五、类与接口
## Item 37: Compose Classes Instead of Nesting Many Levels of Built-in Types(不要用嵌套的内置类型实现多层结构，而应该通过组合起来的类来实现)
### 1. 用 嵌套的内置类型 实现多层结构 的缺点是？
&emsp;&emsp; 多层的嵌套会使其它开发者很难看懂，而且后面维护起来也很麻烦。就像书中的例子一样，要考虑学科、分数 以及该次考试的权重，如果使用内置类型嵌套的话最终的结构为：`{student_name: {subject: [ (weight, score) ] } }`。显然这样的结构很难维护。
&emsp;&emsp; 一般超过两层结构的话就不应该使用这种嵌套类型，而应该通过类来重构。

### 2. 如何解决上面的困境呢？
&emsp;&emsp; 可以用类来重构
```python
from collections import namedtuple, defaultdict

Grade = namedtuple('Grade', ('score', 'weight'))

class Subject:
    def __init__(self):
        self._grades = []
    
    def report_grade(self, score, weight):
        self._grades.append(Grade(score, weight))

    def average_grade(self):
        total_score, total_weight = 0, 0
        for grade in self._grades:
            total_score += grade.score * grade.weight
            total_weight += grade.weight
        return total_score / total_weight


class Student:
    def __init__(self):
        self._subject = defaultdict(Subject)

    def get_subject(self, name):
        return self._subject[name]

    def average_grade(self):
        count, total = 0, 0
        for subject in self._subject.values():
            total += subject.average_grade()
            count += 1
        return total / count

class Gradebook:
    def __init__(self):
        self._students = defaultdict(Student)

    def get_student(self, name):
        return(self._students[name])

book = Gradebook()
albert = book.get_student('Albert Einstein')
math = albert.get_subject('Math')
math.report_grade(75, 0.05)
math.report_grade(65, 0.15)
math.report_grade(70, 0.80)
gym = albert.get_subject('Gym')
gym.report_grade(100, 0.40)
gym.report_grade(85, 0.60)
print(albert.average_grade())
```
运行结果：
```
80.25
```
**结果分析：**
&emsp;&emsp; 通过类将代码重构后，代码量增加了，但是可读性提高了，而且后面维护起来也更方便。






&emsp;
&emsp;
&emsp;
## Item 38: Accept Functions Instead of Classes for Simple Interfaces(让简单的接口接受函数，而不是类的实例)







&emsp;
&emsp;
&emsp;
## Item 39: Use @classmethod Polymorphism to Construct Objects Generically(通过`@classmethod`多态来构造同一体系中的各类对象)
### 1. 
没看明白，后面回来看。TODO: 







&emsp;
&emsp;
&emsp;
## Item 40: Initialize Parent Classes with super(通过`super()`来初始化父类)
### 1. 有哪几种初始化父类的方法？
有两种方法：
> ① 通过`类名.__init__()`；
> ② 通过`super()`；
> 

### 2. 上面的几种初始化父类的方法中，更推荐哪种方法？为什么？
更推荐用`super()`来初始化父类：
> 在**简单的类体系**中，两种方法都不会有问题；
> 但是在**复杂的类体系(菱形继承)**中，，通过`类名.__init__()`来初始化父类会出现重复调用爷爷类的`__init__()`被多次调用的情况，使用`super()`可以避免；具体例子可以看 [类基础.md](grammar/09.%20类基础.md)
> 







&emsp;
&emsp;
&emsp;
## Item 41: Consider Composing Functionality with Mix-in Classes(考虑使用`mixin`类来表示可组合的功能)
### 1. 什么时候应该使用`mixin`类？
&emsp;&emsp; 如果既想通过多重继承来方便地封装逻辑，又想避开多重继承可能带来的问题，这个就应该把 需要被继承的类 写成`Mixin`类。







&emsp;
&emsp;
&emsp;
## Item 42: Prefer Public Attributes Over Private Ones(优先考虑用public属性表示应受保护的数据，而不是用`private`)
### 1. 为什么应该避免使用`private`？
&emsp;&emsp; 把成员变量设为`private`会导致程序难以扩展。

### 2. 对于那些不希望被别人访问的成员，我们应该怎么做？
&emsp;&emsp; 用单下划线`_`，并用文档加以解释。

### 3. 那什么时候适合用`private`？
当子类和父类的成员变量有可能重名的时候，可以考虑使用`private`。







&emsp;
&emsp;
&emsp;
## Item 43: Inherit from collections.abc for Custom Container Types(自定义的容器类型应该从`collections.abc`继承)
### 1. `collections.abc`提供的是什么？
&emsp;&emsp; `collections.abc`中定义了很多跟容器和迭代器 (序列、映射、集合等) 有关的抽象基类。 当我们需要里面的某项功能时，我们可以继承这些抽象基类。

### 2. 假设一个类，我们想让他支持`dict`那样的关键字索引操作(如`obj[key]`)，应该怎么做？
① 自己实现全套功能
② 继承 `collections.abc`中的`Mapping`抽象类

### 3. 为什么更推荐直接继承`collections.abc`中的抽象类，而不是自己实现呢？
&emsp;&emsp; ① 自己写需要多写很多代码，比如想实现`set`的的所有方法，开发者需要编写需要代码，但是直接继承`collections.abc.Set`可以节省绝大部分工作；
&emsp;&emsp; ② 自己写很容易漏功能，而且出现bug的概率比较高

### 4. 定义一个二叉树类，让它支持内置`list`一样的操作
```python
from collections.abc import Sequence

class BinaryNode:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

class IndexableNode(BinaryNode):
    def _traverse(self):
        if self.left is not None:
            yield from self.left._traverse()
        yield self
        if self.right is not None:
            yield from self.right._traverse()

    def __getitem__(self, index):
        for i, item in enumerate(self._traverse()):
            if i == index:
                return item.value
        raise IndexError(f'Index {index} is out of range')

class SequenceNode(IndexableNode):
    def __len__(self):
        for count, _ in enumerate(self._traverse(), 1):
            pass
        return count

class BetterNode(SequenceNode, Sequence):
    pass

tree = BetterNode(
    10,
    left=IndexableNode(
        5,
        left=IndexableNode(2),
        right=IndexableNode(
            6,
            right=IndexableNode(7)
        )
    ),
    right=IndexableNode(
        15,
        left=IndexableNode(11)
    )

)


print('Index of 7 is', tree.index(7))
print('Count of 10 is', tree.count(10))
```
运行结果：
```
Index of 7 is 3
Count of 10 is 1
```







&emsp;
&emsp;
&emsp;
# 六、元类与属性
## Item 44: Use Plain Attributes Instead of Setter and Getter Methods(用纯属性与修饰器取代旧式的`setter`和`getter`方法)
&emsp;&emsp; 主要介绍了`@property`的应用，这些在[类高级.md](grammar/10.%20类高级.md)中已经总结过了。







&emsp;
&emsp;
&emsp;
## Item 45: Consider @property Instead of Refactoring Attributes(考虑用`@property`实现新的属性访问逻辑，不要着急重构代码)
### 1. 课文中讲了啥？
首先，我们用普通的Python对象实现带有 配额(quota)的漏桶(leaky bucket)，这个类可以记录当前的配额以及这份配额的保质期：
```python
from datetime import datetime, timedelta

class Bucket:
    def __init__(self, period):
        self.period_delta = timedelta(seconds=period) # 保质期
        self.reset_time = datetime.now()              # 油是什么时候添加的
        self.quota = 0                                # 配额

    def __repr__(self):
        return f'Bucket(quota={self.quota})'

# 加油
def fill(bucket, amount):
    now = datetime.now()
    if (now - bucket.reset_time) > bucket.period_delta:
        bucket.quota = 0
        bucket.reset_time = now
    bucket.quota += amount

# 消耗油
def deduct(bucket, amount):
    now = datetime.now()
    if (now - bucket.reset_time) > bucket.period_delta:
        return False # Bucket hasn't been filled this period
    if bucket.quota - amount < 0:
        return False # Bucket was filled, but not enough
    bucket.quota -= amount
    return True # Bucket had enough, quota consumed

bucket = Bucket(60)
fill(bucket, 100)
print(bucket)

if deduct(bucket, 99):
    print('Had 99 quota')
else:
    print('Not enough for 99 quota')
print(bucket)    
```
运行结果：
```
Bucket(quota=100)
Had 99 quota
Bucket(quota=1)
```
上面的代码可以正常运行，**但是有一个缺点**：我们只知道剩下多少配额，不知道初始配额。
于是我们重构这个类，把当前时间内的 初始额度(`NewBucket.max_quota`)和 已使用的额度(`NewBucket.quota_consumed`)给记录下来，然后再增加一个`@property`属性`quota`，重构后的代码：
```python
from datetime import datetime, timedelta

class NewBucket:
    def __init__(self, period):
        self.period_delta = timedelta(seconds=period)
        self.reset_time = datetime.now()
        self.max_quota = 0
        self.quota_consumed = 0

    def __repr__(self):
        return (f'NewBucket(max_quota={self.max_quota}, '
                f'quota_consumed={self.quota_consumed})')

    @property
    def quota(self):
        return self.max_quota - self.quota_consumed
    
    @quota.setter
    def quota(self, amount):
        delta = self.max_quota - amount
        if amount == 0:
            # Quota being reset for a new period
            self.quota_consumed = 0
            self.max_quota = 0
        elif delta < 0:
            # Quota being filled for the new period
            assert self.quota_consumed == 0
            self.max_quota = amount
        else:
            # Quota being consumed during the period
            assert self.max_quota >= self.quota_consumed
            self.quota_consumed += delta


def fill(bucket, amount):
    # 同上，略... 

def deduct(bucket, amount):
    # 同上，略... 

bucket = NewBucket(60)
fill(bucket, 100)
print(bucket)

if deduct(bucket, 99):
    print('Had 99 quota')
else:
    print('Not enough for 99 quota')
print(bucket)    
```
运行结果：
```
NewBucket(max_quota=100, quota_consumed=0)
Had 99 quota
NewBucket(max_quota=100, quota_consumed=99)
```

### 2. 作者想通过上面的例子表达什么？
&emsp;&emsp; 通过上面的重构，`fill()`函数和`deduct()`函数都可以继续使用，而且那些使用旧类`Bucket`也依然可以正常使用，这样就做到了最小程度的修改代码。
&emsp;&emsp; 通过上面的例子我们可以知道，`@property`的一个很大的有点就是可以 逐渐完善数据模型而不影响已经写好的代码。

### 3. 既然`@property`可以做到在不影响现有代码的情况下，对数据模型进行重构，那是不是只要一致用`@property`就行了？
&emsp;&emsp; `@property`很好用，但是也不能滥用，如果你发现自己总是扩充`@property`方法，那说明你应该重构这个类了，此时就不要继续在沿着糟糕的方案继续写下去了。







&emsp;
&emsp;
&emsp;
## Item 46: Use Descriptors for Reusable @property Methods(用描述符来改写需要复用的`@property`方法)
### 1. 内置的`@property`有何缺点？
&emsp;&emsp; 内置的`@property`有个明显的缺点：**不能复用，受它修饰的方法，无法为同一个类中的其他属性所复用，而且与之无关的类也无法复用这些方法。**
&emsp;&emsp; 举个例子，要编写一个类，验证学生的家庭作业成绩在0~100之间：
```python
class Homework:
    def __init__(self):
        self._grade = 0

    @property
    def grade(self):
        return self._grade

    @grade.setter
    def grade(self, value):
        if not (0 <= value <= 100):
            raise ValueError('Grade must be between 0 and 100')
        self._grade = value

galileo = Homework()
galileo.grade = 95
```
上面的代码简单易用，但假设我们还需要些一个类记录学生的考试成绩，而且需要把每一科的成绩记录下来：
```python
class Exam:
    def __init__(self):
        self._writing_grade = 0
        self._math_grade = 0
        
    @staticmethod
    def _check_grade(value):
        if not (0 <= value <= 100):
            raise ValueError('Grade must be between 0 and 100')

    @property
    def writing_grade(self):
        return self._writing_grade

    @writing_grade.setter
    def writing_grade(self, value):
        self._check_grade(value)
        self._writing_grade = value

    @property
    def math_grade(self):
        return self._math_grade

    @math_grade.setter
    def math_grade(self, value):
        self._check_grade(value)
        self._math_grade = value            
```
上面的这种写法很费事，因为每科的成绩都需要一套`@property`，假如后面需要增加学科，我们还得为这个学科增加一个`@property`。

### 2. 如何解决这个缺点？
#### 2.1 版本一：
&emsp;&emsp; 在Python中，这样的功能最好通过描述符来实现，
```python
class Descriptor:
    def __init__(self):
        self._grade = 0

    def __get__(self, instance, instance_type):
        return self._grade

    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError('Grade must be between 0 and 100')
        self._grade = value

class Exam:
    _math_grade = Descriptor()
    writing_grade = Descriptor()
    science_grade = Descriptor()
    def __init__(self, n):
        self.name = n

```
下面来跑一下试试：
```python
jack = Exam("jack")

jack._math_grade = 88
jack.writing_grade = 99

print(jack._math_grade)
print(jack.writing_grade)
```
运行结果：
```
88
99
```
似乎一切正常。但是这有一个问题没有暴露：
```python
jack = Exam("jack")

jack._math_grade = 88
jack.writing_grade = 99

print(jack._math_grade)
print(jack.writing_grade)

print("*"*20)

lucy = Exam("Lucy")
print(lucy._math_grade)
print(lucy.writing_grade)
```
运行结果：
```
88
99
********************
88
99
```
**结果分析：**
&emsp;&emsp; 我们可以看到，明明我们没有给`lucy._math_grade`和`lucy.writing_grade`赋值，但是它俩却却和`jack._math_grade`和`jack.writing_grade`实例共性了同一个`Descriptor`实例，这是因为：
> `_math_grade`、`writing_grade` 、`science_grade`都是类属性，它们只在定义`Exam`类的时候被定义一次，而不是 每创建一个`Exam`实例就会有一个新的`Descriptor`和`_math_grade`、`writing_grade` 、`science_grade`对应。
> 

#### 2.2 版本二：
**为了解决这个问题，我们必须把每个`Exam`实例在这个属性上面的取值的记下来，这个我们可以通过字典来实现：**
```python
class Descriptor:
    def __init__(self):
        self._grades = {} # 改动1：_grades改成了一个字典

    def __get__(self, instance, instance_type):
        if instance is None:
            return self
        # 改动2：key 为 Exam实例 本身，通过 get 解决键值不存在的情况
        return self._grades.get(instance, "Unset")

    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError('Grade must be between 0 and 100')
        
        self._grades[instance] = value

class Exam:
    _math_grade = Descriptor()
    writing_grade = Descriptor()
    science_grade = Descriptor()
    def __init__(self, n):
        self.name = n

jack = Exam("jack")

jack._math_grade = 88
jack.writing_grade = 99

print(jack._math_grade)
print(jack.writing_grade)

print("*"*20)

lucy = Exam("Lucy")
lucy.writing_grade = 100
print(lucy._math_grade)
print(lucy.writing_grade)
```
运行结果：
```
88
99
********************
Unset
100
```
**结果分析：**
&emsp;&emsp; 可以看到的是，各个实例之间不再不再互相影响。
#### 2.3 版本三：
上面的版本二存在内存泄漏的问题：
> 在程序的运行中，传给`__set__()`方法的那些`Exam`实例全都被 字典`_grades` 所引用，于是指向那些实例的引用数量就永远不会降到0，这就导致垃圾回收器没办法把那些实例占的空间释放。
> 
为了解决内存泄漏的问题，我们可以使用python内置的 `weakref`模块，该模块里面有一个特殊的字典`WeakKeyDictionary`，它有如下的特殊之处：
> 如果运行时解释器发现`WeakKeyDictionary`中的`key`的引用只剩下一个，而这个引用又是`WeakKeyDictionary`自己的`key`发起的，那么系统会将该引用从这个特殊的字典里删掉，于是这个`key`的引用计数就变成了0，后面垃圾回收器就会把这个`key`释放掉。
> 
```python
from weakref import WeakKeyDictionary

class Descriptor:
    def __init__(self):
        self._grades = WeakKeyDictionary() # 使用 WeakKeyDictionary

    def __get__(self, instance, instance_type):
        ...

    def __set__(self, instance, value):
        ...
```







&emsp;
&emsp;
&emsp;
## Item 47: Use __getattr__, __getattribute__, and __setattr__ for Lazy Attributes(针对懒惰属性使用`__getattr__, __getattribute__, __setattr__`)
&emsp;&emsp; 本节就是介绍了这三个特性，这些已在[类高级.md](grammar/10.%20类高级.md)中有详细的介绍。







&emsp;
&emsp;
&emsp;
## Item 48 - Item 51
TODO: 关于元类的后面回来看







&emsp;
&emsp;
&emsp;
# 第七章、Concurrency and Parallelism(并发与并行)
## Item 52: Use subprocess to Manage Child Processes(用`subprocess`管理子进程)
&emsp;&emsp; 书中只是对`subprocess`进行了简单介绍。







&emsp;
&emsp;
&emsp;
# 第八章、稳定与性能
## Item 65: Take Advantage of Each Block in `try/except/else/finally`(合理利用`try/except/else/finally`结构中的每个代码块)
&emsp;&emsp; 文中就是介绍了一下异常处理的几种方法，已在其它地方做过了笔记。







&emsp;
&emsp;
&emsp;
## Item 66: Consider contextlib and with Statements for Reusable try/finally Behavior（考虑用`contextlib`和`with`语句来改写可复用的`try/finally`）
### 1. 这一节主要介绍了什么？
① 介绍了另一种实现 上下文管理器的方法(使对象支持`with`)：`contextlib.contextmanager`
② 介绍了怎么使用 上下文管理器 来实现可复用的`try/finally`

### 2. 使用 上下文管理器 来实现可复用的`try/finally`
```python
import logging

def my_function():
    logging.debug('Some debug data')
    logging.error('Error log here')
    logging.warning('warning log here')
    logging.info('info log here')
    logging.debug('More debug data')

my_function()
```
运行结果：
```
ERROR:root:Error log here
WARNING:root:warning log here
```
**结果分析：**
&emsp;&emsp; 可以看到的是，只输出了`warning`级别以上的日志，因为默认的级别是`warning`。

如果想暂时的提升日志级别，我们可以通过上下文管理器来做到：
```python
import logging
from contextlib import contextmanager

def my_function():
    logging.debug('Some debug data')
    logging.error('Error log here')
    logging.warning('warning log here')
    logging.info('info log here')
    logging.debug('More debug data')

@contextmanager
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield # 进入with时，with语句会将函数推进到这，然后返回
    finally:
        logger.setLevel(old_level)

with debug_logging(logging.DEBUG):
    print('* Inside:')
    my_function()

print('\n* After:')
my_function()
```
运行结果：
```
* Inside:
DEBUG:root:Some debug data
ERROR:root:Error log here
WARNING:root:warning log here
INFO:root:info log here
DEBUG:root:More debug data

* After:
ERROR:root:Error log here
WARNING:root:warning log here
```
如果希望`with`语句返回一个值，可以将这么写：
```python
@contextmanager
def log_level(level, name):
    logger = logging.getLogger(name)
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield logger
    finally:
        logger.setLevel(old_level)
```







&emsp;
&emsp;
&emsp;
## Item 67: Use datetime Instead of time for Local Clocks（用`datetime`处理本地时间，而不是`time`）
&emsp;&emsp; 进行时区转换的时候，建议使用`datetime`（一般配合`pytz`模块使用），更简单，而且不容易出错。







&emsp;
&emsp;
&emsp;
## Item 68: Make pickle Reliable with copyreg （使用`copyreg`实现可靠的`pickle`操作）
### 1. `pickle`操作存在什么问题？
&emsp;&emsp; 例如，下面这个对象表示玩家在游戏中的进度，其中记录了玩家当前级别(level)、还剩下几条命(lives)：
```python
import pickle

class GameState:
    def __init__(self):
        self.level = 0
        self.lives = 4

state = GameState()
state.level += 1 # Player beat a level
state.lives -= 1 # Player had to try again
print(state.__dict__)

# 退出游戏前，写入文件中
state_path = 'game_state.bin'
with open(state_path, 'wb') as f:
    pickle.dump(state, f)

# 恢复游戏，读取进度
with open(state_path, 'rb') as f:
    state_after = pickle.load(f)    
print(state_after.__dict__)    
```
运行结果：
```
{'level': 1, 'lives': 3}
{'level': 1, 'lives': 3}
```
但是，上面的代码存在一个问题，就是不能很好的应对将来对游戏功能的扩展，比如我们还想记录玩家的最好成绩，于是我们需要给`GameState`类增加一个字段：
```python
class GameState:
    def __init__(self):
        self.level = 0
        self.lives = 4
        self.points = 0 # New field
```
用`pickle`对新版本的`GameState`对象做序列化是没有问题的，但有一个问题，就是我们从文件中恢复的`GameState`对象是没有`points`属性的：
```python
import pickle

class GameState:
    def __init__(self):
        self.level = 0
        self.lives = 4
        self.points = 0 # New field

state_path = 'game_state.bin'
# 读取
with open(state_path, 'rb') as f:
    state_after = pickle.load(f)    
print(state_after.__dict__)    
```
运行结果：
```
{'level': 1, 'lives': 3}
```
**结果分析：**
&emsp;&emsp; 可以看到的是，从文件中`pickle`出来的`GameState`对象是没有`points`属性的。

### 2. 如何解决上面的问题？
&emsp;&emsp; 利用`copyreg`模块可以解决上面的问题，具体做法直接翻书。







&emsp;
&emsp;
&emsp;
## Item 69: Use decimal When Precision Is Paramount(在需要准确计算的场合，用`decimal`表示相应的数值)
### 1. python的浮点数存在什么问题？
python的浮点数存在 精度问题，来看一个小例子：
```python
num = 0.0
for i in range(10):
    print(num := num + 0.1)
```
运行结果：
```
0.1
0.2
0.30000000000000004
0.4
0.5
0.6
0.7
0.7999999999999999
0.8999999999999999
0.9999999999999999
```
**结果分析：**
&emsp;&emsp; 可以看到，输出确实存在精度问题。

### 2. 为什么python的浮点数会有这样的问题？
&emsp;&emsp; 这种问题不仅在 Python 中存在，在所有支持浮点数运算的编程语言中都会遇到，具体原因在CSAPP里面有讲到TODO:

### 3. 如何解决这个问题？
在精度要求高的场合，使用`Decimal`类来解决：
```python
from decimal import Decimal

num = Decimal('0.0')
for i in range(10):
    print(num := num + Decimal('0.1'))
```
运行结果：
```
0.1
0.2
0.3
0.4
0.5
0.6
0.7
0.8
0.9
1.0
```

### 4. 使用`Decimal`类时要注意什么？
指定`Decimal`的初始化有两种方法：
> ① 把含有数值的`str`字符串传给`Decimal`的构造函数；
> ② 直接把`float`或`int`传给`Decimal`的构造函数
> 
但是我们来看一段代码：
```python
from decimal import Decimal

print(Decimal('1.23'))
print(Decimal(1.23))
```
运行结果：
```
1.23
1.229999999999999982236431605997495353221893310546875
```
可以看到的是，直接把`float`传给`Decimal`的构造函数还是会出现偏差，因为`1.23`在python的浮点机制中会存在偏差，因此传给`Decimal`的构造函数的值也有偏差。
**因此，如果想要准确答案，那么应该使用`str`字符串来构造`Decimal`。**

### 5. 对小数进行四舍五入
#### 5.1 `round()`函数
使用内置函数`round( x [, n]  )`，参数：
> **x** -- 数值表达式。
> **n** -- 数值表达式，表示从小数点位数。
> 

```python
print(round(123.55))
print(round(123.55,0))
print(round(123.55,1))
print(round(123.55,-1))
```
运行结果：
```
124
124.0
123.5
120.0
```

#### 5.2 自定义小数的舍入规则
&emsp;&emsp; `round()`函数对四舍五入的控制不够精确，而`Decimal`类的`quantize()`方法可以提供更为精确的控制，通过对`quantize()`方法指定`rounding`参数，我们可以自定义舍入规则，`rounding`参数可指定为如下几种：
> decimal.ROUND_CEILING ： 舍入方向为 Infinity。
> decimal.ROUND_DOWN ： 舍入方向为零。
> decimal.ROUND_FLOOR ： 舍入方向为 -Infinity。
> decimal.ROUND_HALF_DOWN ： 舍入到最接近的数，同样接近则舍入方向为零。
> decimal.ROUND_HALF_EVEN ： 舍入到最接近的数，同样接近则舍入到最接近的偶数。
> decimal.ROUND_HALF_UP ： 舍入到最接近的数，同样接近则舍入到零的反方向。
> decimal.ROUND_UP ： 舍入到零的反方向。
> decimal.ROUND_05UP  ： 如果最后一位朝零的方向舍入后为 0 或 5 则舍入到零的反方向；否则舍入方向为零。
> 
假如我们希望采用 **进一法** 的舍入规则，可以这么写：
```python
from decimal import ROUND_UP, Decimal

cost = 5.363

rounded = cost.quantize(Decimal('0.01'), rounding=ROUND_UP)
print(f'quantize() {cost} to {rounded}')
print(f'round() {cost} to {round(cost, 2)}')
```
运行结果：
```
quantize() 5.365 to 5.37
round() 5.365 to 5.36   
```
**结果分析：**
&emsp;&emsp; 可以看到的是，`cost = 5.363`，小数点后第三位为`3`，按理来说应该要舍去的，但是采用`quantize()`方法，我们将舍入规则改为了 进一法。







&emsp;
&emsp;
&emsp;
## Item 70: Profile Before Optimizing(先分析性能，然后再进行优化)
### 1. 为什么需要分析性能再进行优化？
&emsp;&emsp; 因为在Python中，哪些模块耗时最多，凭直觉不靠谱，应该先用工具分析以下哪些代码耗时最多，然后针对性的进行优化，这样才能事半功倍。

### 2. 利用工具分析代码性能
#### 2.1 在Python中，一般用什么工具分析代码性能？更推荐使用哪个？
> &emsp;在Python中，普遍用下面两种工具来分析代码性能：
> &emsp;&emsp; ① `profile` : 纯Python版本实现；
> &emsp;&emsp; ② `cProfile`: C扩展版本
> 
一般更推荐使用`cProfile`，因为它对受测程序影响最小，测评结果更准确。

#### 2.2 分析程序的性能时，需要注意什么？
&emsp;&emsp; 在分析性能的时候，一定要把和外部交互的那部分代码(访问外部网络或读写硬盘) 和 核心代码 区分开来，因为会受到带宽的影响，测评结果不准确。

#### 2.3 如何使用`cProfile`进行性能分析？

```python
from random import randint
from cProfile import Profile

def insertion_sort(data):
    result = []
    for value in data:
        insert_value(result, value)
    return result

def insert_value(array, value):
    for i, existing in enumerate(array):
        if existing > value:
            array.insert(i, value)

max_size = 10**4
data = [randint(0, max_size) for _ in range(max_size)]
test = lambda:insertion_sort(data)

profiler = Profile()
profiler.runcall(test)

from pstats import Stats

stats = Stats(profiler)
stats.strip_dirs()
stats.sort_stats('cumulative')
stats.print_stats()
```
运行结果：
```
         10003 function calls in 0.013 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.013    0.013 practice.py:17(<lambda>)
        1    0.005    0.005    0.013    0.013 practice.py:4(insertion_sort)
    10000    0.007    0.000    0.007    0.000 practice.py:10(insert_value)
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```
简单介绍下上面测评结果每一列的含义：
> &emsp;&emsp; ■ **ncalls**: The number of calls to the function during the profiling period.
> &emsp;&emsp; ■ **tottime**: The number of seconds spent executing the function,excluding time spent executing other functions it calls.
> &emsp;&emsp; ■ **tottime percall**: The average number of seconds spent in the function each time it is called, excluding time spent executing other functions it calls. This is tottime divided by ncalls.
> &emsp;&emsp; ■ **cumtime**: The cumulative number of seconds spent executing the function, including time spent in all other functions it calls.
> &emsp;&emsp; ■ **cumtime percall**: The average number of seconds spent in the function each time it is called, including time spent in all other functions it calls. This is cumtime divided by ncalls.
> 
根据上面的分析结果，我们可以知道，`insert_value()`是最耗时的那一个，因此它是我们优化的重点。
&emsp;&emsp; 我们可以用内置的`bisect`模块来重新实现`insert_value()`：
```python
from bisect import bisect_left

def insert_value(array, value):
    i = bisect_left(array, value)
    array.insert(i, value)
```
运行结果：
```
         30003 function calls in 0.041 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.041    0.041 practice.py:17(<lambda>)
        1    0.005    0.005    0.041    0.041 practice.py:4(insertion_sort)
    10000    0.008    0.000    0.036    0.000 practice.py:11(insert_value)
    10000    0.017    0.000    0.017    0.000 {method 'insert' of 'list' objects}
    10000    0.011    0.000    0.011    0.000 {built-in method _bisect.bisect_left}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```







&emsp;
&emsp;
&emsp;
## Item 71: Prefer deque for Producer–Consumer Queues(优先考虑使用`deque`实现 生产者-消费者队列(即`FIFO`))
### 1. 为什么`deque`比`list`更适合实现`FIFO`？
这和它俩的底层结构有关系：
> &emsp;&emsp; **`list`** 的底层实现是数组(准确的说是动态数组，支持动态增长，有点像C++的`vector`)，当我们对其进行`pop(n)`的时候，解释器会把`n`后面的元素都往前移动一个位置；
> &emsp;&emsp; **`deque`**的底层是链表，`pop(n)`的时候时间是固定的。
> 







&emsp;
&emsp;
&emsp;
## Item 72: Consider Searching Sorted Sequences with bisect(考虑用`bisect`搜索已排序的序列)
&emsp;&emsp; 本节介绍了Python标准库自带的 二分搜索算法`bisect.bisect_left()`（bisection algorith(二分法)），因为它用的是二分法，所以自然要比 对序列中的每个元素进行比较 这种方法要快。
&emsp;&emsp; 其实这个库里不仅仅有`bisect.bisect_left()`函数，还有其它版本的二分搜索算法，甚至还有二分插入算法，具体可以看官方文档。







&emsp;
&emsp;
&emsp;
## Item 73: Know How to Use heapq for Priority Queues(使用`heap`制作优先级队列)
### 1. 优先级队列(priority queue)是什么？
&emsp;&emsp; `deque`是`FIFO`队列，但是有的时候我们希望 队列里的元素 按优先级来排列，在这种情况下就应该使用 优先级队列。

### 2. 如何使用 优先级队列？
&emsp;&emsp; 使用python的内置模块`heap`可以做到。但是使用`heap`模块的时候需要注意，添加到优先队列中的元素必须支持比较操作，这个我们可以通过 类修饰器`@functools.total_ordering`，并把描述小于关系的`__lt__()`定义出来即可：
```python
import functools

@functools.total_ordering
class Book:
    def __init__(self, title, due_date):
        self.title = title
        self.due_date = due_date

    def __lt__(self, other):
        return self.due_date < other.due_date
```







&emsp;
&emsp;
&emsp;
## Item 74: Consider memoryview and bytearray for Zero-Copy Interactions with bytes(考虑使用 `memoryview`和`bytearray` 来实现 零拷贝的`bytes`操作)
### 1. 缓冲协议(buffer protocol)
#### .1 缓冲协议 的作用是？
&emsp;&emsp; 缓冲区协议 允许一个对象公开其内部数据(缓冲区)，而另一个对象可以访问这些缓冲区而无需中间复制。
&emsp;&emsp; 但我们只能在C-API级别上访问此协议，而不能使用我们的常规代码库。因此，为了将相同的协议公开给普通的Python代码库，需要使用内存视图。

#### 2.2 哪些对象支持缓冲协议？
&emsp;&emsp; 内建类型`bytes` 和 `bytearray`，扩展类型 `array.array`都支持。
&emsp;&emsp; 第三方库也可能会为了特殊的目的而定义它们自己的类型，例如用于图像处理和数值分析等。

### 2. 内存视图`memoryview`
#### 2.1 `memoryview`的作用是？
&emsp;&emsp; `memoryview`是一个类，它的构造函数`memoryview()`返回给定参数的内存查看对象(memory view)，这个内存查看对象会对支持缓冲区协议的数据进行包装，可以在不需要复制对象基础上使用Python代码进行访问。
&emsp;&emsp; 上面的讲解都太官方了，其实可以把`memoryview`看成是系统提供的一套零拷贝的结构，通过`memoryview`可以避免内存复制（当然仅限于支持缓存协议的类型），提高处理效率。

#### 2.2 如何使用 `memoryview`？
##### (1) 构造函数
```cpp
class memoryview(obj)
```
创建一个引用 `obj`对象 的 `memoryview` 。 `obj`对象 必须支持缓冲区协议。内建类型`bytes` 和 `bytearray`，扩展类型 `array.array`都支持缓冲区协议。
##### (2) 类属性
官方文档
##### (3) 类方法
见官方文档。

##### (4) 使用实例
① 通过 `view = memoryview(obj)`来建立到`obj`的内存映射`view`
② 操作内存映射对象`view`（如切片等），这样才能发挥内存映射的作用：不触发内存复制；
③ 
④ 

#### 2.3 `memoryview`一般在什么场景下使用？
&emsp;&emsp; 正如前面说的，对于那些支持缓冲协议的对象，`memoryview`的好处是不会有内存拷贝：
```python
data = bytearray(b'0123456789')

print('直接对data进行切片')
a = data[:5]
a[0] = 0x70
a[1] = 0x70
print(a)
print(data)

print('\n先对data建立内存视图，然后再进行切片')
view = memoryview(data)
a = view[:5]
a[0] = 0x70
a[1] = 0x70
print(a.tobytes())
print(data)
```
运行结果：
```
直接对data进行切片
bytearray(b'pp234')
bytearray(b'0123456789')

先对data建立内存视图，然后再进行切片
b'pp234'
bytearray(b'pp23456789')
```
**结果分析：**
可以看到的是，
> &emsp;&emsp; ① 如果直接对`data`进行切片，会生成一个新的对象`a`，因此对`a`的修改不会影响到`data`；
> &emsp;&emsp; ② 但是如果先对`data`建立内存视图，然后再进行切片，对`a`的修改也会影响到`data`。
> 
显然，`memoryview`不会触发内存拷贝，这在一些需要处理大量内存数据的程序来说，可以大大的节省效率，比如`socket`编程。

#### 2.4 `memoryview`构建的内存视图 和 原对象是什么关系？
&emsp;&emsp; 其实可以把内存视图看成是原对象在内存上的映射，这就意味着 通过内存视图对内存进行修改会影响到原对象：
```python
data = bytearray(b'shave and a haircut, two bits')

view = memoryview(data) # 构建 内存视图
chunk = view[0:10]

print(chunk.tobytes())
print(view.tobytes())
print(data)
print('*'*15)

chunk[0] = 0x70 # 通过内存视图对内存进行修改
chunk[1] = 0x70 
chunk[2] = 0x70 

print(chunk.tobytes())
print(view.tobytes())
print(data)
```
运行结果：
```
b'shave and '
b'shave and a haircut, two bits'
bytearray(b'shave and a haircut, two bits')
***************
b'pppve and '
b'pppve and a haircut, two bits'
bytearray(b'pppve and a haircut, two bits')
```
**结果分析：**
&emsp;&emsp; 可以看到的是，通过内存视图对内存进行修改 会影响到原对象。

#### 2.5 `memoryview`对象能否修改？
这个要看，`memoryview`映射的对象是否是可修改的，来看代码：
```python
data = bytes(b'shave and a haircut, two bits')

view = memoryview(data) # 构建 内存视图
chunk = view[0:10]

chunk[0] = 0x70 # 通过内存视图对内存进行修改
```
运行结果：
```
Traceback (most recent call last):
  File "f:\code\python\test\test.py", line 6, in <module>
    chunk[0] = 0x70 # 通过内存视图对内存进行修改
TypeError: cannot modify read-only memory
PS F:\code\python\test> 
```
**结果分析：**
&emsp;&emsp; 在上面的代码中，`bytes`是不可修改的，因此通过`chunk`对其修改会报错。


### 3. `bytearray`
#### 3.1 `bytearray` 和 `bytes`有何异同？
&emsp;&emsp; `bytes`是不可修改的，`bytearray`可以理解为
&emsp;&emsp; 

#### 3.2 什么场景需要使用 `bytearray` ？
文件IO的时候经常用得上：
> &emsp;&emsp; 在读取IO数据流的时候，如果使用`bytes`类型接收数据流，那么每次读取一段内容都会生成一个新的对象，每次都需要重新分配内存。
> &emsp;&emsp; 但是`bytearray`不一样，因为`bytearray`是可变对象，有内存分配机制，每次分配内存时都会多分配一点，因此 使用它接收数据流可以减少内存的分配次数。
> 

### 4. 使用`memoryview`和`bytearray`来构建流媒体服务器
&emsp;&emsp; 使用`memoryview`和`bytearray`可以构建零拷贝。
#### 4.1 发送数据
&emsp;&emsp; 通过建立`memoryview`，然后对其进行切片，然后直接发给对端，这样就避免了内存复制。

#### 4.2 接收数据
&emsp;&emsp; `socket.recv_into()`可以使用缓冲协议来迅速接收数据，他会把接收到的内容直接写入缓冲区，我们可以把`memoryview`所制作的切片传给它，这样就能直接替换底层的数据了。

#### 4.3 实例
```python
size = 10*1024*1024 # 一次发10mb
send_data = ... # 待发送的数据
recv_cash = ... # 接收缓存

def send2client(sock, offset):
    send_view = memoryview(send_data)   # 建立到send_data的内存视图
    send_chunk = send_view[offset : offset + size] # 需发送的数据块
    sock.send(send_chunk)

def recv_from_client(sock, offset): 
    recv_array = bytearray(recv_cash) 
    recv_view = memoryview(recv_array)
    recv_chunk = recv_view[offset : offset + size] 
    sock.recv_into(recv_chunk) # 注意要使用recv_into()
```

### 参考文献
1. [bytes/bytearray/memoryview](https://zhuanlan.zhihu.com/p/399946068)
2. [Python使用Zero-Copy和Buffer Protocol实现高性能编程](https://www.cnblogs.com/erhuabushuo/p/10314803.html)
3. [求解释一下python中bytearray和memoryview 的使用 以及适用的场景](https://segmentfault.com/q/1010000007137721)








&emsp;
&emsp;
&emsp;
# 第九章 测试与调试
## tem 75: Use repr Strings for Debugging Output(通过`repr`字符串输出调试信息)
### 1. `__str__`和`__repr__`的作用




① 
② 
③ 
④ 

```python

```
运行结果：
```

```
**结果分析：**
&emsp;&emsp; 


# 参考文献
1. [Python3中的bytes和str类型](https://zhuanlan.zhihu.com/p/102681286)