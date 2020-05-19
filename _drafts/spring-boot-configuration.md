---
layout: post
title: "spring boot configuration"
categories: spring-boot configuration
---

## 例子

```
@SpringBootConfiguration
public class Configuration {
    @Bean
    public Map createMap(){
        Map map = new HashMap();
        map.put("username","zhihao.miao");
        map.put("age",27);
        return map;
    }
}
```

```
public static void main(String[] args) {
    ConfigurableApplicationContext context = SpringApplication.run(DemoApplication.class, args);
    context.getBean(Runnable.class).run();
    System.out.println(context.getBean(User.class));
    Map map = (Map) context.getBean("createMap");
    int age = (int) map.get("age");
    System.out.println("age=="+age);
}
```
