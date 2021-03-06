## 是什么
Haskell是一门纯函数式编程语言。
命令式编程语言是通过命令描述怎么做，函数式编程语言是通过函数来描述做什么。
函数式编程语言中的函数能做的唯一事情就是求值，因而没有副作用。
若以同样的参数调用同一函数两次，得到的结果总是相同。这被称作“引用透明”。
Haskell是惰性的。也就是说若非特殊指明，函数在真正需要结果以前不会被求值。再加上引用透明，你就可以把程序仅看作是数据的一系列变形。
惰性语言中的计算只是一组初始数据和变换公式。
Haskell 是静态类型的。并拥有一套强大的类型系统，支持自动类型推导。

## 基础
#### 算数运算
```
(1 + 2) * 3 / (-4)
```
#### 逻辑运算
```
True && False

True || False

not True
```
#### 函数
*就是一个将两个数相乘的函数，就像三明治一样，用两个参数将它夹在中央，这被称作中缀函数。
而其他大多数不能与数夹在一起的函数则被称作前缀函数。
函数调用的形式是函数名，空格，空格分隔的参数表。
```
succ 8

min 9 10 

max 9 10 
```
如果某函数有两个参数，也可以用 ` 符号将它括起，以中缀函数的形式调用它
```
3 `div` 2
```
#### 定义函数
```
doubleMe x = x + x 
```
定义在.hs文件中
保存为any.hs或任意名称，然后转至保存的位置，打开ghci，执行:l any.hs
另一种形式的函数
```
conanO'Brien = "It's a-me, Conan O'Brien!"  
```
没有参数的函数通常被称作“定义”（或者“名字”），一旦定义，conanO'Brien就与字符串"It's a-me, Conan O'Brien!"完全等价，且它的值不可以修改。
ps：首字母大写的函数是不允许
#### 组合函数
```
doubleUs x y = doubleMe x + doubleMe y  
```
#### if
```
doubleSmallNumber x = if x > 100 then x else  x*2 
doubleSmallNumber' x = (if x > 100 then x else x*2) + 1 
```
注意函数名最后的那个单引号，它没有任何特殊含义，只是一个函数名的合法字符罢了。通常使用单引号来区分一个稍经修改但差别不大的函数。

