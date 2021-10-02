---
layout: post
title: "SpringBoot DevTools"
categories: springBoot DevTools
---

## 添加依赖
在 build.gradle 的 dependencies 中加入
```
developmentOnly 'org.springframework.boot:spring-boot-devtools'
```

## 自动重启应用程序
应用程序被加载到JVM中的两个单独的类加载器中。
- 一个类装入器装入 Java 代码、属性文件以及项目的 src/main/path 中的几乎所有东西。这些部分可能会频繁更改。
- 另一个类加载器加载了依赖库，它们不太可能经常更改。  
当检测到更改时，DevTools 只重新加载包含项目代码的类加载器，并重新启动 Spring 应用程序上下文，但不影响其他类加载器和 JVM。  
这意味着，每当在构建规范中添加、更改或删除依赖项时，都需要重新启动应用程序才能使这些更改生效。  

## 自动刷新浏览器和禁用模板缓存
自动启用 LiveReload （http://livereload.com/）服务器和应用程序。
与相应的 LiveReload 浏览器插件相结合时，它会使得浏览器在对模板、图像、样式表、JavaScript 等进行更改时自动刷新。

## 在 H2 控制台中构建
如果选择使用 H2 数据库进行开发，DevTools 还将自动启用一个 H2 控制台。
只需将 web 浏览器指向 http://localhost:8080/h2-console，就可以深入了解应用程序正在处理的数据。

## 配置idea
#### 设置自动编译
command+shift+alt+/ 
![image](/assets/images/spring-boot-start-1.png)
选中Registry...
勾中  
![image](/assets/images/spring-boot-start-2.png)
#### 设置自动build
preferences -> Compile -> Build project automatically -> 选中


