## 寻找重复行

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    counts := make(map[string]int)
    input := bufio.NewScanner(os.Stdin)
    for input.Scan() {
        counts[input.Text()]++
    }
    // NOTE: ignoring potential errors from input.Err()
    for line, n := range counts {
        if n > 1 {
            fmt.Printf("%d\t%s\n", n, line)
        }
    }
}
```

## map

map 存储了键/值（key/value）的集合。键可以是任意类型，只要其值能用==运算符比较，值则可以是任意类型。

```go
counts := make(map[string]int)
```
map是一个由make函数创建的数据结构的引用

## bufio
Scanner类型是该包最有用的特性之一，它读取输入并将其拆成行或单词。
```go
input := bufio.NewScanner(os.Stdin)
for input.Scan() {
    counts[input.Text()]++
}
```
input变量从程序的标准输入中读取内容。每次调用input.Scan()，即读入下一行，并移除行末的换行符；
读取的内容可以调用input.Text()得到。Scan函数在读到一行时返回true，不再有输入时返回false。
## printf
```go
fmt.Printf("%d\t%s\n", n, line)
```
类似于C或其它语言里的printf函数，fmt.Printf函数对一些表达式产生格式化输出。
%d          十进制整数
%x, %o, %b  十六进制，八进制，二进制整数。
%f, %g, %e  浮点数： 3.141593 3.141592653589793 3.141593e+00
%t          布尔：true或false
%c          字符（rune） (Unicode码点)
%s          字符串
%q          带双引号的字符串"abc"或带单引号的字符'c'
%v          变量的自然形式（natural format）
%T          变量的类型
%%          字面上的百分号标志（无操作数）

## File
```go
for _, arg := range files {
    f, err := os.Open(arg)
    if err != nil {
        fmt.Fprintf(os.Stderr, "dup: %v\n", err)
        continue
    }
    countLines(f, counts)
    f.Close()
}

```
os.Open函数返回两个值。第一个值是被打开的文件(*os.File），其后被Scanner读取。

os.Open返回的第二个值是内置error类型的值。如果err等于内置值nil（译注：相当于其它语言里的NULL），那么文件被成功打开。
读取文件，直到文件结束，然后调用Close关闭该文件，并释放占用的所有资源。