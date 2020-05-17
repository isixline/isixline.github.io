---
layout: post
title:  "gradle wrapper"
categories: gradle wrapper
---

## 定义
运行Gradle的环境和版本会对构建结果带来不确定性。    
gradle wrapper是gradle包装器。它是一个脚本，可以在计算机没有安装Gradle的情况下运行Gradle构建，并且能够指定Gradle的版本。    
当使用gradle wrapper启动Gradle时，如果指定版本的gradle没有被下载关联，会先从Gradle官方仓库下载该版本Gradle到用户本地，进行解包并执行批处理文件。后续的构建运行都会重用这个解包的运行时安装程序。    

## 构建gradle wrapper
在本地已经有gradle的情况下，执行  
```
gradle wrapper
```
会在根目录下生成：
- gradle
    - wrapper
        - gradle-wrapper.jar（包含gradle运行时的逻辑代码）
        - gradle-wrapper.properties（负责配置包装器运行时行为的属性文件，用来配置使用哪个版本的Gradle等属性）
- gradlew（Linux平台下，用于执行Gralde命令的包装器脚本）
- gradlew.bat（Windows平台下，用于执行Gralde命令的包装器脚本）
一些参数：
- --gradle-version：用于下载和执行指定的gradle版本。
- --distribution-type：指定下载Gradle发行版的类型，可用选项有bin和all，默认值是bin，-bin发行版只包含运行时，但不包含源码和文档。

## 配置gradle wrapper
gradle-wrapper.properties是用来配置gradle wrapper属性的文件
```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.2.1-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

字段含义：
- distributionBase：Gradle解包后存储的主目录。
- distributionPath：distributionBase指定目录的子目录。
- distributionUrl：Gradle发行版压缩包的下载地址。
- zipStoreBase：Gradle压缩包存储主目录。
- zipStorePath：zipStoreBase指定目录的子目录。

## 使用gradle wrapper
是使用gradlew或gradlew.bat脚本
```
./gradlew taskName
```
