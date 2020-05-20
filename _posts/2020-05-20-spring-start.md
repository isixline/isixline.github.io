---
layout: post
title:  "spring 起步"
categories:  spring
---

## 什么是spring？
Spring的核心是一个容器，通常称为 Spring 应用程序上下文，用于创建和管理应用程序组件。
#### bean
bean是一个由Spring IoC容器实例化、组装和管理的对象。  
bean在Spring应用程序上下文中连接在一起以构成一个完整的应用程序。  
将bean连接在一起的行为是基于一种称为依赖注入（DI）的模式。容器创建并维护所有的bean，并将其注入需要它们的bean中。
#### 注册bean
```
@Configuration
public class ServiceConfiguration {
    @Bean
    public InventoryService inventoryService() {
        return new InventoryService();
    }
    
    @Bean
    public ProductService productService() {
        return new ProductService(inventoryService());
    }
}
```
@Configuration注释向Spring表明这是一个配置类，它将为Spring应用程序上下文提供beans。   
配置的类方法带有@Bean注释，指示它们返回的对象应作为beans添加到应用程序上下文中。  
#### 自动配置
即自动装配和组件扫描。
- 组件扫描，Spring可以自动从应用程序的类路径中发现组件，并将其创建为Spring应用程序上下文中的bean。
- 自动装配，Spring 会自动将组件与它们依赖的其他bean一起注入。

## spring boot
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


#### 程序入口
@SpringBootApplication注解包含：
- @SpringBootConfiguration。该注解继承自@Configuration，二者功能也一致，标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到srping容器中，并且实例名就是方法名。
- @EnableAutoConfiguration。启动自动的配置，Springboot根据你添加的jar包来配置你项目的默认配置，比如根据spring-boot-starter-web ，来判断你的项目是否需要添加了webmvc和tomcat，就会自动的帮你配置web项目中所需要的默认配置。
- @ComponentScan。扫描当前包及其子包下被@Component，@Controller，@Service，@Repository注解标记的类并纳入到spring容器中进行管理。

#### main()
main()方法使用Spring Boot的SpringApplication.run()方法来启动应用程序。

## 创建一个Controller
```
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello(@RequestParam(value = "name", defaultValue = "world") String name) {
        return String.format("hello     %s", name);
    }
}
```
#### @RestController
自动将返回值转化为json返回
#### @RequestMapping
“路由”映射。将具有“/hello”的http请求对应到hello方法
#### @RequestParam
路由参数（/hello?name=anybody）
- value，路由参数名
- defaultValue，默认值


## 执行
#### 执行main()
执行标记有@SpringBootApplication注解的类中的main()
#### ./gradlew bootRun
#### 运行jar文件
- 构建JAR文件./gradlew build，默认输出在build/libs文件夹。
- 执行JAR文件
    ```
    java -jar build/libs/demo-0.0.1-SNAPSHOT.jar
    ```
