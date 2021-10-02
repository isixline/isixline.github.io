---
title: Bean Validation date: 2021-10-01 00:00 tags:

- java

---

## 是什么

先看一段代码，在业务中经常会有对请求值的校验，最直接的方式，堆积if，一个个参数进行校验。  
可以满足需要，但是不够优雅简洁，也难以形成标准。

```java
public class UserCreateRequest {
    private String email;

    private String name;

    private String password;

    public void validate() {
        if (this.email == null) {
            throw xxxx;
        }
        if (this.name == null) {
            throw xxxx;
        }
        ....
    }
}
```

Bean Validation就定义了一套规范，是一套基于注解的数据校验规范，定义了用于JavaBean验证的元数据模型和API。 再看下代码

```java
public class UserCreateRequest {
    @NotEmpty
    @GeneralEmail
    private String email;

    @NotBlank
    private String name;

    @NotBlank
    private String password;
}
```

## 引入依赖

如果spring-boot版本小于2.3.x，spring-boot-starter-web会自动传入hibernate-validator依赖。  
如果spring-boot版本大于2.3.x，则需要手动引入依赖：org.springframework.boot:spring-boot-starter-validation。    
spring-boot-starter-validation是对hibernate-validation的二次封装， 而hibernate-validation是对该规范的实现，并增加了校验注解如@Email、@Length等。

## 一些常用注解

```java
@NotNull // 不为null
@NotEmpty // String、Collection、Map，不为null，并且长度大于0
@NotBlank // String不为null，并且trim后长度大于0

@Size(min = x, max = x) // String、Collection、Map，长度在min和max范围内
@Length(min = x, max = x) // String，长度在min和max范围内

@Max(value) // 数字、数字格式String，大于等于value
@Min(value) // 数字、数字格式String，小于等于value
@Range(min = x, max = x) // 数字、数字格式String，数值在min和max范围内

@Future // 将来某个日期
@Past // 过去某个日期
@PastOrPresent // 过去及现在某个日期

@Pattern // 满足给定的正则表达式
@Email // 满足Email格式

// 这些我也不知道哪些情况会被用，判断是不是一个具体的值
@AssertFalse // 所注解的元素必须是Boolean类型，且值为false
@AssertTrue // 所注解的元素必须是Boolean类型，且值为true
@Null // 所注解的元素值为null
```

## 开启验证
@Validated是@Valid的变体  
@Validated：可以用在类型、方法和方法参数上。但是不能用在成员属性（字段）上  
@Valid：可以用在方法、构造函数、方法参数和成员属性（字段）上  

#### 对RequestBody验证

```java
  public UserCreateResponse createUser(@RequestBody @Valid UserCreateRequest request){
        // xxxx
        }
```
或者
```java
  public UserCreateResponse createUser(@RequestBody @Validated UserCreateRequest request){
        // xxxx
        }
```

#### 对RequestParam验证

```java
@Validated
public class UserManagementController {
    public Page<QueryUserResponse> query(@RequestParam(required = false) @NotBlank String keyword,
                                         Pageable pageable) {
        // xxxx
    }
}
```

## 捕获异常

最后，我们再收个尾，将数据校验抛出的异常进行捕获，返回给前端简单清晰够用的返回值即可。   
异常response的返回格式与前端达成一致就好，这里提供一种简单的返回格式供参考

```Java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<Map<String, List<String>>>handleMethodArgumentNotValidException(MethodArgumentNotValidException e){
        List<String> fieldErrors=e.getBindingResult().getFieldErrors().stream()
        .map(fieldError->fieldError.getField()+" "+fieldError.getDefaultMessage())
        .collect(Collectors.toList());

        Map<String, List<String>>map=new HashMap<>();
        map.put("errors",fieldErrors);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(map);
        }

```
