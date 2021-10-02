
---
layout: post
title:  "Python 数据操作"
categories:  Python
---

## 切片
list[start:end]
取下标为start到end-1的数据作为列表返回。
- start省略为0
- end省略为列表长度
- 倒数第一个元素的索引是-1
- 复制列表L[:]
- 元组和字符串都可以看作列表

## 迭代
可迭代对象：
- 列表
- 元组
- 字符串
- 字典（默认情况下，dict迭代的是key。如果要迭代value，可以用for value in d.values()，如果要同时迭代key和value，可以用for k, v in d.items()）
判断是否是可迭代对象
```
from collections import Iterable
isinstance('abc', Iterable)
```
Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：
```
for i, value in enumerate(['A', 'B', 'C']):
    print(i, value)
```

## 列表生成式
举个例子，要生成list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]可以用list(range(1, 11))
要生成list[1, 4, 9, 16...]，可以用[x * x for x in range(1, 11)]
筛选偶数，可以用 [x * x for x in range(1, 11) if x % 2 == 0]
在一个列表生成式中，for前面的if ... else是表达式，而for后面的if是过滤条件，不能带else。

## 生成器
如果列表元素可以按照某种算法推算出来，那么可以在循环的过程中不断推算出后续的元素，这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。
```
g = (x * x for x in range(10))
next(g)
```
generator保存的是算法，每次调用next(g)，就计算出g的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出StopIteration的错误。
正确的方法是使用for循环，因为generator也是可迭代对象。
```
for n in g:
    print(n)
```

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```
这就是定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator

## 迭代器
把list、dict、str等Iterable变成Iterator可以使用iter()函数：
```
isinstance(iter('abc'), Iterator)
```



