---
layout: post
title: "gradle 起步"
categories: gradle
---

## 定义

Gradle 是以 Groovy 语言为基础, 基于DSL (领域特定语言) 语法的自动化构建工具。  
本身只提供了一个构建项目的框架，真正起作用的是 plugin。

- Project：由一些组件组成，可以代表一个 JAR 库或者一个 WEB 应用程序，也可能包含其他项目生成的 JAR 包。
- Task：不可分的最小工作单元，执行构建工作（比如编译项目或执行测试）。

## build.gradle

gradle 构建脚本。
每个 gradle build 由一到多个 Project 组成。  
每个 Project 由一到多个 Task 组成。  
定义一个名为 hello 的 task。