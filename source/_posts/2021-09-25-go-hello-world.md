---
title: go hello world
date: 2021-09-25 00:01
tags:
  - go
---

## install
#### 安装
```shell
brew install go
```
#### 检查版本
```shell
go version
```

## hello world
```go
package main

import "fmt"

func main()  {
	fmt.Println("Hello Word")
}
```

## run

```
go run main.go
```

## build

```
go build main.go
```

## 组织结构

- package：一个包由位于单个目录下的一个或多个.go 源代码文件组成, 目录定义包的作用。每个源文件都以一条 package 声明语句开始，
- .go文件   
  main 包比较特殊，它定义了一个独立可执行的程序，而不是一个库。  
  在 main 里的 main 函数是整个程序执行时的入口。  
  必须恰当导入需要的包，缺少了必要的包或者导入了不需要的包，程序都无法编译通过必须恰当导入需要的包。  

## gofmt

Go 语言在代码格式上采取了很强硬的态度。  
gofmt 工具把代码格式化为标准格式，并且 go 工具中的 fmt 子命令会对指定包, 否则默认为当前目录, 中所有.go 源文件应用 gofmt 命令。

## goimports

可以根据代码需要, 自动地添加或删除 import 声明。  
获取goimports
```
go get golang.org/x/tools/cmd/goimports
```
