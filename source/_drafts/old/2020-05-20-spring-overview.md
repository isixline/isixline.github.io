---
layout: post
title:  "Spring 总览"
categories:  Spring overview
---

## Spring 核心库
Spring 核心框架是 Spring 领域中其他一切的基础。它提供了核心容器和依赖注入框架。以及一些其他的基本特性。
其中包括了：
- Spring MVC 和 Spring web 框架
- 一些基本数据持久性支持，特别是基于模板的 JDBC 支持。
- 添加了对响应式编程的支持，包括一个新的响应式 web 框架 —— Spring WebFlux。

## Spring Boot
但除了启动依赖和自动配置，Spring Boot 还提供了一些其他有用的特性：
- Actuator 提供了对应用程序内部工作方式的运行时监控
- 在核心框架的测试辅助之外，还有额外的测试支持。
- 提供了一种基于 Groovy 脚本的替代编程模型，称为 Spring Boot CLI（命令行界面）。可以将整个应用程序编写为 Groovy 脚本的集合，并从命令行运行它们。

## Spring Data
将应用程序的数据存储库抽象为简单的 Java 接口，同时当定义方法用于如何驱动数据进行存储和检索的问题时，对方法使用了命名约定。
能够处理几种不同类型的数据库，包括关系型（JPA）、文档型（Mongo）、图型（Neo4j）等。
- Spring Data JPA - 针对关系数据库的持久化
- Spring Data Mongo - 针对 Mongo 文档数据库的持久化
- Spring Data Neo4j - 针对 Neo4j 图形数据库的持久化
- Spring Data Redis - 针对 Redis 键值存储的持久化
- Spring Data Cassandra - 针对 Cassandra 数据库的持久化

## Spring Security
解决了广泛的应用程序安全性需求，包括身份验证、授权和 API 安全性。

## Spring Integration 和 Spring Batch
在某种程度上，大多数应用程序将需要与其他应用程序集成，甚至需要与同一应用程序的其他组件集成。
为了满足这些需求，出现了几种应用程序集成模式。Spring Integration 和 Spring Batch 为基于 Spring 的应用程序提供了这些模式的实现。
Spring Integration 解决了实时集成，即数据在可用时进行处理。
相反，Spring Batch 解决了批量集成的问题，允许在一段时间内收集数据，直到某个触发器（可能是一个时间触发器）发出信号，表示该处理一批数据了。

## Spring Cloud
微服务是一个热门话题，解决了几个实际的开发和运行时问题。Spring Cloud 是一组用 Spring 开发云本地应用程序的项目。




