---
layout: post
title: "spring boot 起步"
categories: springBoot
---

## 设计目的

用来简化新 Spring 应用的初始搭建以及开发过程。  
开箱即用，提供各种默认配置来简化项目配置。

## 基本结构

- src/main/java 程序开发以及主程序入口
- src/main/resources 配置文件
- src/test/java 测试程序
  spring boot 建议的主程序结构
- Application.java 建议放到根目录下面,主要用于做一些框架配置
- model 主要用于实体与数据访问层（Repository）
- service 业务类代码
- controller 负责页面访问控制

## 添加依赖

- spring-boot-starter ：核心模块，包括自动配置支持、日志和 YAML（spring-boot-starter-web 自动依赖了 spring-boot-starter）
- spring-boot-starter-test ：测试模块，包括 JUnit、Hamcrest、Mockito

## hello world

```
@RestController
public class HelloWorldController {
    @RequestMapping("/hello")
    public String hello() {
        return "Hello World";
    }
}

```

## 热部署

在 build.gradle 的 dependencies 中加入

```
developmentOnly 'org.springframework.boot:spring-boot-devtools'
```

#### 配置idea
###### 设置自动编译
command+shift+alt+/ 
![image](/assets/images/spring-boot-start-1.png)
选中Registry...
勾中  
![image](/assets/images/spring-boot-start-2.png)
###### 设置自动build
preferences -> Compile -> Build project automatically -> 选中


