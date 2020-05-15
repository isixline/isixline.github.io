---
layout: post
title: "gradle 命令行"
categories: gradle command
---

## 多任务调用
依次调用 compile 和 test 任务, 它们所依赖的任务也会被调用. （重复依赖的任务只执行一次）
```
gradle compile test 
```

## 任务排除
dist任务依赖test任务，排除test任务
```
gradle dist -x test
```

## 失败后继续执行构建
--continue

## 指定构建文件位置
-b 参数用以指定脚本具体所在位置, 格式为 dirpwd/build.gradle.
-p 参数用以指定脚本目录即可

## 查看项目列表
```
gradle projects
```

## 查看任务列表
```
gradle tasks
```
--all 更多信息（包括任务间依赖）

## 获取依赖列表
```
gradle dependencies
```

## 获取项目属性列表
```
gradle properties
```

## 获取具体任务信息
```
 gradle help --task someTask
```

## 获取构建日志
--profile参数可以收集一些构建期间的信息并保存到 build/reports/profile 目录下.
并且会以构建时间命名这些文件