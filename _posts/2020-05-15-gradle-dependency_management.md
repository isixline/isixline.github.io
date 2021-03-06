---
layout: post
title: "gradle 依赖管理"
categories: gradle dependency
---

## 定义
- 依赖项：项目需要构建或运行的东西
- 发行项：需要构建并上传项目所产生的东西

## 依赖配置
#### compile
用来编译项目源代码的依赖.
#### runtime
在运行时被生成的类使用的依赖（默认的, 也包含了编译时的依赖）
#### testCompile
编译测试代码的依赖（默认的, 包含生成的类运行所需的依赖和编译源代码的依赖）
#### testRuntime
运行测试所需要的依赖（ 默认的, 包含上面三个依赖）

## 仓库
默认情况下，Gradle不提前定义任何仓库。
在使用外部依赖之前, 需要自己至少定义一个库。
```
repositories {
    mavenCentral()
}
```
一个项目可以有好几个库. Gradle会根据依赖定义的顺序在各个库里寻找它们, 在第一个库里找到了就不会再在第二个库里找它了.