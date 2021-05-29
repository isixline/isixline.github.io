## os 包

os 包以跨平台的方式，提供了一些与操作系统交互的函数和变量。
程序的命令行参数可从 os 包的 Args 变量获取；os 包外部使用 os.Args 访问该变量。

## echo

```go
package main

import (
	"fmt"
	"os"
	"strings"
)

func main() {
	fmt.Println(strings.Join(os.Args[1:], " "))
}
```

## 变量声明

变量声明类型

```go
var s, sep string
```

短变量声明

```
s := ""
```

## 循环

for 循环

```go
for initialization; condition; post {
    // zero or more statements
}
```

initialization 语句是可选的，在循环开始前执行。必须是一条简单语句（simple statement），即，短变量声明、自增语句、赋值语句或函数调用。
condition 是一个布尔表达式（boolean expression），其值在每次循环迭代开始时计算。如果为 true 则执行循环体语句。
post 语句在循环体执行结束后执行，之后再次对 condition 求值。condition 值为 false 时，循环结束。

```go
for i := 1; i < len(os.Args); i++ {
    s += sep + os.Args[i]
    sep = " "
}
```

for 循环的另一种形式, 在某种数据类型的区间（range）上遍历，如字符串或切片。

```go
for _, arg := range os.Args[1:] {
    s += sep + arg
    sep = " "
}
```
每次循环迭代，range产生一对值；索引以及在该索引处的元素值。
Go语言中这种情况的解决方法是用空标识符（blank identifier），即_（也就是下划线）。
空标识符可用于任何语法需要变量名但程序逻辑不需要的时候。
