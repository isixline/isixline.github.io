---
layout: post
title: "gradle Java构建"
categories: gradle Java
---

## Java 插件

大多数 Java 项目是非常相似的: 编译 Java 源文件, 运行一些单元测试, 同时创建一个包含类文件的 JAR。  
Java 插件是基于合约的. 这意味着插件已经给项目的许多方面定义了默认的参数, 比如 Java 源文件的位置。
使用 Java 插件

```
plugins {
    id 'java'
}
```

默认：

- 在 src/main/java 下寻找到生产源码
- 在 src/test/java 下寻找到测试源码
- 在 src/main/resources 下寻找到准备打包进 jar 的资源文件
- 所有的输出文件都会被创建在构建目录里, 生成的 JAR 文件会被存放在 build/libs 目录下.

## 常用Task

#### build

用于完全构建项目，将会编译和测试代码,并生成一个包含所有类与资源的 JAR 文件。

#### clean

删除 build 目录和所有为 build 生成的文件。

#### assemble

编译并打包代码, 但是并不运行单元测试.其他插件会在这个任务里加入更多的步骤。（举个例子,如果使用 War 插件,这个任务还将根据项目生成一个 WAR 文件）

#### check

编译并测试代码. 其他的插件会加入更多的检查步骤。（举个例子, 如果使用 checkstyle 插件, 这个任务将会运行 Checkstyle 来检查代码）

## 外部依赖
通常, 一个 Java 项目的依赖许多外部的 JAR 文件。
#### 加入maven仓库
```
repositories {
    mavenCentral()
}
```
#### 加入依赖
```
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

## 更改属性
Java 插件给项目加入了一些属性 (propertiy)，这些属性已经被赋予了默认的值。
如果觉得不合适，也可以修改这些值。 
#### 指定项目版本号 
```
version = '1.0'
```
#### 指定Java版本
```
sourceCompatibility = 1.8
```
#### 加入了一些属性在 jar 的manifest中
```
jar {
    manifest {
        attributes 'Implementation-Title': 'Gradle Quickstart', 'Implementation-Version': version
    }
}
```
#### 在测试阶段加入一个系统属性
```
test {
    systemProperties 'property': 'value'
}
```
### 列出项目所有属性
```
gradle properties   
```

## 发布jar文件
#### 定义发布Task
```
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
```
#### 执行Task
```
gradle uploadArchives
```