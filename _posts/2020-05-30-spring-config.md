---
layout: post
title:  "Spring Config"
categories:  Spring Config
---

## 微调自动配置
在 Spring 中有两种不同（但相关）的配置：
- Bean wiring —— 它声明应用程序组件将在 Spring 应用程序上下文中作为 bean 创建，以及它们应该如何相互注入。
- Property injection —— 在 Spring 应用程序上下文中设置 bean 的值。
#### Spring 环境抽象
Spring 环境抽象是任何可配置属性的一站式商店。它抽象了属性的起源，以便需要这些属性的 bean 可以从 Spring 本身使用它们。
- JVM 系统属性
- 操作系统环境变量
- 命令行参数
- 应用程序属性配置文件
然后，它将这些属性聚合到单一的源中，从这个源中可以注入 Spring bean。
通过 Spring Boot 自动配置的 bean 都可以通过从 Spring 环境中提取的属性进行配置。
#### 配置数据源
application.yml
```
spring:
  datasource:
    url: jdbc:mysql://localhost/tacocloud
    username: tacodb
    password: tacopassword
    schema:
    - order-schema.sql
    - ingredient-schema.sql
    - tao-schema.sql
    - user-schema.sql
    data:
    - ingredients.sql
```

#### 配置嵌入式服务器
```
server:
  port: 0
```
设置 server.port 为 0，但是服务器不会在端口 0 上启动。
相反，它将从随机选择的可用端口启动。这在运行自动化集成测试以确保任何并发运行的测试不会在硬编码端口号上发生冲突时非常有用。
```
keytool -keystore mykeys.jks -genkey -alias tomcat -keyalg RSA
```
将其设置为处理 HTTPS 请求。通过使用 JDK 的 keytool 命令行工具创建一个密钥存储。
```
server:
  port: 8443
  ssl:
    key-store: file:///path/to/mykeys.jks
    key-store-password: letmein
    key-password: letmein
```
在这里 server.port 属性设置为 8443，这是开发 HTTPS 服务器的常用选择。
server.ssl.key-store 属性设置为创建密钥存储库文件的路径。
同时 server.ssl.key-store-password 和 server.ssl.key-password 属性都被设置为创建密钥存储时指定的密码值。  
#### 配置日志
默认情况下，Spring Boot 通过 Logback 配置日志，默认为 INFO 级别。
```
logging:
  path: /var/logs/
  file: TacoCloud.log
  level:
    root: WARN
    org:
      springframework:
        security: DEBUG
```
将 root 日志级别设置为 WARN，但是将 Spring 安全日志设置为 DEBUG 级别.
将日志条目写入位于 /var/logs/ 文件夹下的 TacoCloud.log 文件。

#### 使用特殊的属性值
```
greeting:
  welcome: ${spring.application.name}
```
想要设置一个名为 greeting.welcome 的属性，用于返回另一个名为 spring.application.name 的属性的值。
为此，在设置 greeting.welcome 时可以使用 ${} 占位符标记

## 创建自己的配置属性
为了支持配置属性的属性注入，Spring Boot 提供了@ConfigurationProperties 注释。
当放置在任何 Spring bean 上时，它指定可以从 Spring 环境中的属性注入到该 bean 的属性。
```
taco:
  orders:
    pageSize: 10
```
```
@ConfigurationProperties(prefix="taco.orders")
```
在应用程序中，这些 bean 的惟一目的是作为配置数据的持有者，这使控制器和其他应用程序类不涉及特定于配置的细节，它还使得在几个可能使用该信息的 bean 之间共享公共配置属性变得很容易。
```
@Component
@ConfigurationProperties(prefix="taco.orders")
@Data
public class OrderProps {
    private int pageSize = 20;
}
```
OrderProps 收集与一个地方的订单相关的配置属性。如果需要添加、删除、重命名或以其他方式更改其中的属性，只需要在 OrderProps 中应用这些更改。

#### 声明配置属性元数据
配置属性元数据是完全可选的，并不会阻止配置属性的工作。但是元数据对于提供有关配置属性的最小文档非常有用，特别是在 IDE 中。
要为自定义配置属性创建元数据，需要在 META-INF（例如，在项目下的 src/main/resources/META-INF 中）中创建一个名为 addition-spring-configuration-metadata.json 的文件。

## 使用 profile 文件进行配置
当应用程序部署到不同的运行时环境时，通常会有一些配置细节不同。例如，数据库连接的细节在开发环境中可能与在 QA 环境中不一样，在生产环境中可能还不一样。
例如：出于开发和调试的目的，希望使用嵌入式 H2 数据库，并且希望将 Taco Cloud 代码的日志级别设置为 DEBUG。
但是在生产中，需要使用一个外部 MySQL 数据库，并将日志记录级别设置为 WARN。
profile 文件是一种条件配置类型，其中根据运行时激活的 profile 文件应用或忽略不同的 bean、配置类和配置属性。

#### 定义特定profile的属性
文件的名称应该遵循这个约定：application-{profile 名称}.yml 或 application-{profile 名称}.properties。
创建一个application-prod.yml 的新文件
```
spring:
  datasource:
    url: jdbc:mysql://localhost/tacocloud
    username: tacouser
    password: tacopassword
logging:
  level:
    tacos: WARN
```

#### 激活profile文件
```
spring:
  profiles:
    active:
    - prod
```
或者
```
export SPRING_PROFILES_ACTIVE=prod
```
或者
```
java -jar taco-cloud.jar --spring.profiles.active=prod
```
也可以激活多个profile文件
```
export SPRING_PROFILES_ACTIVE=prod,audit,ha
```

#### 有条件地使用 profile 文件创建 bean
假设只有在某个配置文件处于活动状态时才需要创建一些 bean，在这种情况下，@Profile 注解可以将 bean 指定为只适用于给定的 profile 文件。
例如：在 TacoCloudApplication 中声明了一个 CommandLineRunner bean，用于在应用程序启动时加载嵌入式数据库中的成分数据。这对于开发来说很好，但是在生产应用程序中是不必要的（也是不受欢迎的）。
```
@Bean
@Profile("dev")
public CommandLineRunner dataLoader(IngredientRepository repo,
     UserRepository userRepo, PasswordEncoder encoder) {
    ...
}
```
配置多个
```
@Profile({"dev", "qa"})
```
排除
```
@Profile("!prod")
```
