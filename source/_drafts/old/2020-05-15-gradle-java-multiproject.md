---
layout: post
title: "gradle Java多项目构建"
categories: gradle Java multiple
---

## 一个典型的分层布局
```
multiproject/
  api/
  services/webservice/
  shared/
```

##  定义一个项目构建
settings.gradle
```
include "shared", "api", "services:webservice", "services:shared"
```

## 通用配置
对于所有子项目都通用的配置。 
根项目就像一个容器, subprojects 方法遍历这个容器的所有元素并且注入指定的配置。
```
subprojects {
    apply plugin: 'java'
    apply plugin: 'eclipse-wtp'

    repositories {
       mavenCentral()
    }

    dependencies {
        testCompile 'junit:junit:4.11'
    }

    version = '1.0'

    jar {
        manifest.attributes provider: 'gradle'
    }
}
```

## 项目间的依赖
举个例子, 一个项目的 JAR 文件被用来编译另外一个项目。  
在api构建文件中加入一个由shared项目产生的JAR文件的依赖。  
由于这个依赖, Gradle将确保shared项目总是在api之前被构建。  

```
dependencies {
    compile project(':shared')
}
```

## 创建一个发行版