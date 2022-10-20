# 12. 借`shared_ptr`实现`copy-on-write (COW)`
## 12.1 如何用 `shared_ptr`实现`copy-on-write`？
概括的来说，**COW技术的精髓是**：
> (1) 如果你是数据的唯一拥有者，那么你可以直接修改数据。
> (2) 如果你不是数据的唯一拥有者，那么你拷贝它之后再修改。
> 
用`shared_ptr`来实现COW时，主要考虑两点：
> (1) 读数据
> (2) 写数据
> 
&emsp;&emsp; `shared_ptr`拥有对对象的引用计数，在对对象进行读写操作时，这个计数是`1`，当读数据时，我们 创建一个新的智能指针 指向 原智能指针指向的对象，这个时候引用计数加`1`（变为`2`），这么做其实就是为了提醒其它线程，这个对象还有其他人持有，你别乱改。
&emsp;&emsp; **概括的来说**，就是通过新建一个 局部`shared_ptr` 指向共享的那个 全局`shared_ptr`所指向的对象，让其引用计数增加，这样其它线程就能知道你在使用该对象，其它线程就会 “自觉地” 拷贝后再修改：
```cpp
//假设g_ptr是一个全局的shared_ptr<Foo>并且已经初始化。
void read()
{
    shared_ptr<Foo> tmpptr; // 新建一个 局部shared_ptr
    {
        lock(); // 加锁
        /* 此时引用计数为2，这样写端就能知道有其它线程持有了g_ptr，它就不会直接修改g_ptr了
         而是会复制一份，然后修改复制的那一份，具体可以看后面 写端的实现*/
        // 引用计数加1是为了提醒其它线程，这个对象还有其他人持有，你别乱改
        tmpptr=g_ptr; // 局部智能指针tmpptr 和 全局智能指针g_ptr指向同一对象，它俩的引用计数都是2
    }
    // 注意 tmpptr指向的对象的引用计数还是为2哦，因为tmpptr还没被释放
    
    // 访问tmpptr
    //...

} // 离开read()，tmpptr被释放，那么引用计数就减1
```
这部分是shared_ptr最基本的用法，还是很好理解的，`read()`函数调用结束，`tmpptr`作为栈上变量离开作用域，自然析构，原数据对象的引用计数也变为1。
&emsp;&emsp; 写数据就复杂一些。根据COW的准则，当你是唯一拥有者（对应对象的引用计数是1）时，那么你直接修改数据，这样没有问题，当你不是唯一拥有者，则需要拷贝数据再去修改，这就需要用到一些shared_ptr的编程技法了：
```cpp
void write()
{
    // 写端要在一开始就加锁，要不然多个线程并发write()时会导致，某些线程写入失效。
    lock() 
    if(!g_ptr.unique())
    {
        g_ptr.reset(new Foo(*g_ptr));
    }
    assert(g_ptr.unique());
    // write
    // 
}
```
解释一下代码：
`shared_ptr::unique()`：当引用计数为1时返回true，否则false。
那么当引用计数不为1的时候，说明有别的线程正在读，受shread_ptr::reset()中example的误导，一直以为，reset后，原对象被析构，这样不就会影响正在读的线程了吗？
**解释一下`write()`中的`g_ptr.reset(new Foo(*g_ptr));`**
&emsp;&emsp; 假设在同一时刻，有一个读线程A，一个写线程B，当写线程B进入到`if`循环中时，原对象的引用计数为2（读线程A也持有了`g_ptr`指向的数据，导致引用计数加1），分别为`tmpptr`（线程A）和`g_ptr`，此时`reset()`函数将原对象的引用计数减1，并且`g_ptr`已经指向了新的对象（用原对象构造），这样就完成了数据的拷贝，并且原对象还在，只是引用计数变成了1，只有在读端线程A释放了`tmpptr`后才会导致旧的数据被释放。

## 12.2 用`shared_ptr`实现COW时要注意什么呢？
read端：
> 锁只需在拷贝的时候加，最好使用local copy，或者新建一个函数来完成拷贝也是可以的（就像后面的`CustomerData::query`的第一行代码那样）。
> 
write端：
> 锁需要在一开始就加上，因为我们是需要修改共享的资源的，所以我们需要对整个函数加锁。
> 

## 12.3 copy-on-write 应用：用普通`mutex`替换读写锁的一个例子
**场景**： 一个多线程的C++程序，24h x 5.5d运行。有几个工作线程ThreadWorker{0, 1, 2, 3}， 处理客户发过来的交易请求； 另外有一个背景线程ThreadBackground， 不定期更新程序内部的参考数据。 这些线程都跟一个hash表打交道， 工作线程只读， 背景线程读写， 必然要用到一些同步机制， 防止数据损坏。 这里的示例代码用`std::map`代替`hash`表， 意思是一样的：
```cpp
typedef std::pair<string, int> Entry;
typedef std::vector<Entry> EntryList;
typedef std::map<string, EntryList> Map;
```
`Map`的`key`是用户名， `value`是一个`vector`， 里边存的是不同`stock`的最小交易间隔， `vector`已经排好序， 可以用二分查找。
&emsp;&emsp; 我们的系统要求工作线程的延迟尽可能小，可以容忍背景线程的延迟略大。一天之内，背景线程对数据更新的次数屈指可数，最多一小时一次，更新的数据来自于网络，所以对更新的及时性不敏感。Map的数据量也不大，大约一千多条数据。
&emsp;&emsp; 最简单的同步办法是用读写锁：工作线程加读锁，背景线程加写锁。但是读写锁的开销比普通mutex要大，而且是写锁优先，会阻塞后面的读锁。如果工作线程能用最普通的非重入`mutex`实现同步，就不必用读写锁，这能降低工作线程延迟。我们借助`shared_ptr`做到了这一点： 
```cpp
class CustomerData : boost::noncopyable
{
public:
    CustomerData()
        : data_(new Map)
    { }

    int query(const string& customer, const string& stock) const;

private:
    typedef std::pair<string, int> Entry;
    typedef std::vector<Entry> EntryList;
    typedef std::map<string, EntryList> Map;
    typedef boost::shared_ptr<Map> MapPtr;
    void update(const string& customer, const EntryList& entries);
    void update(const string& message);

    static int findEntry(const EntryList& entries, const string& stock);
    static MapPtr parseData(const string& message);

    MapPtr getData() const
    {
        muduo::MutexLockGuard lock(mutex_);
        return data_;
    }

    mutable muduo::MutexLock mutex_;
    MapPtr data_;
};
```
`CustomerData::query()`相当于读端，用了前面说的引用计数加`1`的办法， 用局部`MapPtr data`变量来持有`Map`，防止并发修改：
```cpp
int CustomerData::query(const string& customer, const string& stock) const
{
    // getData() 返回 data_的拷贝，这样管理data_的shared_ptr的引用计数就
    // 不再为1，而是2，如果写端(这里是背景线程)同时在操作data_，
    // 它就能知道有人持有 _data，它会复制一份再修改。
    MapPtr data = getData();

    Map::const_iterator entries = data->find(customer);
    if (entries != data->end())
        return findEntry(entries->second, stock);
    else
        return -1;
}
```
&emsp;&emsp; 关键看`CustomerData::update()`（写端）怎么写。既然要更新数据，那肯定得加锁，如果这时候其他线程正在读， 那么不能在原来的数据上修改， 得创建一个副本，在副本上修改，修改完了再替换。如果没有用户在读， 那么就能直接修改，节约一次Map拷贝。
&emsp;&emsp; 注意其中用了`shared_ptr::unique()`来判断是不是有人在读， 如果有人在读， 那么我们不能直接修改， 因为`query()`并没有全程加锁， 只在`getData()`内部有锁。`shared_ptr::swap()`把`data_`替换为新副本， 而且我们还在锁里，不会有别的线程来读， 可以放心地更新。如果别的reader线程已经刚刚通过`getData()`拿到了`MapPtr`，它会读到稍旧的数据。这不是问题，因为数据更新来自网络，如果网络稍有延迟， 反正reader线程也会读到旧的数据。
```cpp
// 每次收到一个 customer 的数据更新
void CustomerData::update(const string& customer, const EntryList& entries)
{
    // 注意，写端要在最外层加锁，要不然多个线程同时update()会导致某些线程修改失败
    muduo::MutexLockGuard lock(mutex_); //
    if (!data_.unique()) // 如果本线程不是 data_ 的唯一持有者，那就复制一份再修改
    {
        // 复制一份
        MapPtr newData(new Map(*data_)); 
        data_.swap(newData);
    }
    assert(data_.unique());
    (*data_)[customer] = entries;
}

void CustomerData::update(const string& message)
{
    MapPtr newData = parseData(message);
    if (newData)
    {
        muduo::MutexLockGuard lock(mutex_);
        data_.swap(newData);
    }
}
```