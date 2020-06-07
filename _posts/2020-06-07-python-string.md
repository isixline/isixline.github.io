
---
layout: post
title:  "Python String"
categories:  Python String
---

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

## 字符编码
在Python3中字符串是以Unicode编码的。  
对于单个字符的编码，Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符。
Python对bytes类型的数据用带b前缀的单引号或双引号表示：
```
x = b'ABC'
```
以Unicode表示的str通过encode()方法可以编码为指定的bytes
```
'ABC'.encode('ascii')
```
如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法：
```
b'ABC'.decode('ascii')
```

## 格式化
```
'Hello, %s' % 'world'
```
在字符串内部，%s表示用字符串替换，%d表示用整数替换，%f表示用浮点数替换，
有几个%?占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个%?，括号可以省略。
```
print('%2d-%02d' % (3, 1))
print('%.2f' % 3.1415926)
```
格式化整数和浮点数还可以指定是否补0和整数与小数的位数。  
```
Age: %s. Gender: %s' % (25, True)
```
如果你不太确定应该用什么，%s永远起作用，它会把任何数据类型转换为字符串。  
```
growth rate: %d %%' % 7
```
转义，用%%来表示一个%。
```
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
```
另一种格式化字符串的方法是使用字符串的format()方法，它会用传入的参数依次替换字符串内的占位符{0}、{1}……
