---
layout: post
title:  "Python overview"
categories:  Python
---

## 安装python3
Mac 自带 python2   
安装python3
```
brew install python3
```

## python 命令环境
#### 进入命令环境
```
python3
```
#### 退出命令环境
```
exit()
```
或
crl + d


## Hello world
#### 命令行
```
print('Hello world')
```
#### 通过python解释器执行python文件
```
python3 hello.py
```
#### 直接执行python文件
```
#!/usr/bin/env python3

print('Hello world')
```
```
chmod a+x hello.py
./hello.py
```

## 输入和输出
#### 输入
```
name = input()
```
在按下回车前输入的内容都会以字符串的形式存储在name中
```
name = input('Please enter your name: ')
```
提示语
#### 输出
```
print('a')
```
```
print('a', 'b', 'c')
```
多个字符串时，输出默认以空格分隔（如：'a b c'）

## 注释
```
# 这是一段注释
```

## 缩进
```
a = 100
if a >= 0:
    print(a)
else:
    print(-a
```
以缩进标识代码块

## 数据类型
## 整数
十进制：0，1，-10
十六进制：0xff00（用0x前缀和0-9，a-f表示）
## 浮点数
小数：0.1，-11.1
科学计数法：1.23e9（1.23x10<sup>9</sup>），1.2e-5（0.000012）
## 字符串
```
print('abc')
print("abc")
print("I'm good!")
print('I\'m \"OK\"!')
print(r'\t')
print('''line1
line2
line3''')
```
## 布尔值
```
True and False
True or False
not True
```
## 空值
None

ps: Python的整数没有大小限制，浮点数也没有大小限制，但是超出一定范围就直接表示为inf（无限大）。

## 变量与常量
#### 变量
变量名是大小写英文、数字和_的组合，且不能用数字开头
```
a = 'ABC'
b = a
a = 123
```
Python解释器做了这几件事情：
1. 在内存中创建了一个'ABC'的字符串。
2. 在内存中创建了一个名为a的变量，并把它指向'ABC'。
3. 在内存中创建了一个名为b的变量
4. 把b指向a所指向的数据
5. 在内存中创建了一个123的整数
6. 把a指向123
最后print(a)为123，print(b)为'ABC'
#### 常量
通常用全部大写的变量名表示常量。 
```
PI = 3.14159265359
```
但事实上PI仍然是一个变量，Python根本没有任何机制保证PI不会被改变

## list
list是一种有序的集合，可以随时添加和删除其中的元素
#### 基本操作
```
classmates = ['Michael', 'Bob', 'Tracy']
```
```
len(classmates)
```
```
classmates[-1]
```
```
classmates.append('Adam')
```
```
classmates.insert(1, 'Jack')
```
```
classmates.pop()
```
```
classmates.pop(1)
```
#### 元素数据类型
```
L = ['Apple', 123, True]
```
```
s = ['python', 'java', ['asp', 'php'], 'scheme']
```

## tuple
另一种有序列表叫元组：tuple。tuple和list非常类似，但是tuple一旦初始化就不能修改。  
因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。
```
t = (1, 2)
```
```
t = (1,)
```

## dict
dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。
```
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
print(d['Michael'])
d['Adam'] = 67
```
判断key是否存在
```
'Thomas' in d

d.get('Thomas')
d.get('Thomas', -1)
```
删除key-value
```
d.pop('Bob')
```
dict的key必须是不可变对象，如字符串、整数。
这是因为dict根据key来计算value的存储位置。

## set
set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。
```
s = set([1, 1, 2, 2, 3, 3])
```
重复元素在set中自动被过滤。
```
s.add(4)
s.remove(4)
```
set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：
```
s1 & s2

s1 | s2
```


## 条件判断
```
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```
```
if x:
    print('True')
```
只要x是非零数值、非空字符串、非空list等，就判断为True，否则为False。

## 循环
#### for...in
依次把list或tuple中的每个元素迭代出来
```
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
```
```
sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum = sum + x
print(sum)
```
生成一个整数序列
```
list(range(5))
```
#### while
```
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```
break：提前结束循环。
continue：跳过当前的这次循环，


## 函数
#### 内置函数
数学计算
```
abs(-100)

max(1, 2, 3)

min(1, 2, 3)
```
数据类型转换
```
int('123')
int(12.3)

float('12.3')

str(1.23)

bool(1)
```
类型检查
```
isinstance(x, (int, float))
```
#### 定义函数
```
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```
空函数
```
def nop():
    pass
```
返回多个值
```
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```
返回值是其实是一个tuple！但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值。
##### 默认参数
```
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```
必选参数在前，默认参数在后。
当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。
默认参数降低了函数调用的难度，而一旦需要更复杂的调用时，又可以传递更多的参数来实现。无论是简单调用还是复杂调用，函数只需要定义一个。
#### 可变参数
```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

 calc(1, 3, 5, 7)
```
可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。
#### 关键字参数
而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。
```
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

person('Adam', 45, gender='M', job='Engineer')
```
如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下：
```
def person(name, age, *, city, job):
    print(name, age, city, job)

def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```
和关键字参数**kw不同，命名关键字参数需要一个特殊分隔符*，*后面的参数被视为命名关键字参数。






