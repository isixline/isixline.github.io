---
layout: post
title: "spring boot 起步"
categories: springBoot
---

## 设计目的

用来简化新 Spring 应用的初始搭建以及开发过程。  
开箱即用，提供各种默认配置来简化项目配置。

## 新建项目
#### https://start.spring.io/
#### 添加gradle依赖
gradle.build文件
```
plugins {
	id 'org.springframework.boot' version '2.3.0.RELEASE'
	id 'io.spring.dependency-management' version '1.0.9.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

test {
	useJUnitPlatform()
}

```
- spring-boot-starter ：核心模块，包括自动配置支持、日志和 YAML（spring-boot-starter-web 自动依赖了 spring-boot-starter）
- spring-boot-starter-test ：测试模块，包括 JUnit、Hamcrest、Mockito

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

#### 创建一个Controller
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


