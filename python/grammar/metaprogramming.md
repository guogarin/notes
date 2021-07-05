## 为什么下面的代码不需要调用就有结果？
```python
# 没有引入任何包
map_f = {}

def logged(a):
    def decorator(func):
        map_f[a] = func
    
    return decorator

@logged("NUMBER")
def hello():
    pass

for i in map_f:
    print("%s : %s" % (i, map_f[i]))
```
运行结果：
> NUMBER : <function hello at 0x0000022759E781E0>

