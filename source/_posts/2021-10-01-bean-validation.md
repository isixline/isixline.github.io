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

## 自定义验证注解
这里举个🌰，@AccountName为验证为phone或者email格式
```java
@Documented
@Constraint(validatedBy = AccountNameValidator.class)
@Target({FIELD, PARAMETER})
@Retention(RUNTIME)
public @interface AccountName {
    String message() default "Invalid account name";

    Class[] groups() default {};

    Class[] payload() default {};
}
```
```java
public class AccountNameValidator implements ConstraintValidator<AccountName, String> {
    @Override
    public boolean isValid(String accountName, ConstraintValidatorContext context) {
        return GeneralEmailValidator.valid(accountName) || PhoneValidator.valid(accountName);
    }
}
```

## 开启验证
#### @Validated和@Valid
@Validated是@Valid的变体    
先说@Valid，它标记方法参数，方法返回值及其中的成员属性，并级联地进行验证，这意味着每当使用它标记一个参数时，参数中的各个属性都会被校验。  
举个🌰，当使用@Valid验证UserUpdateRequest作为方法参数时，会验证UserUpdateRequest中的name和address，然后级联验证Address中的province和city
```java
public class UserUpdateRequest {
    @NotBlank
    private String name;

    @NotNull
    @Valid 
    private Address address;

    public static class Address {
        @NotBlank
        private String province;
        
        @NotBlank
        private String city;
    }
}
```
再说@Validated，和@Valid使用上差不多，但有两点需要注意：
- 不能用在成员属性上
- 提供了一个分组功能，可以在入参验证时，根据不同的分组采用不同的验证机制

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
这里就有个有趣的事情，为什么不可以直接在方法参数上使用@Valid或@Validated，而是要在类上使用@Validated？   
若直接在方法参数上使用@Valid或@Validated，那么会验证该参数的内部属性，而不会关心验证该参数被表示标识的验证。    
而@Validated有个特别的地方，当被放在spring bean的类上，spring会为其创建一个切面代理，对于其所有的public方法，如果方法有声明需要验证的参数，则进行验证。  
另外，@Validated也可以放到方法上，但没有创建切面的功能，仅仅是用来声明该方法所有的需要验证的参数适用的group，并覆盖类上提供的分组。  

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

@ExceptionHandler(ConstraintViolationException.class)
public ResponseEntity<Map<String, List<String>>> handleConstraintViolationException(ConstraintViolationException e) {
        List<String> errors = e.getConstraintViolations().stream()
        .map(constraintViolation -> constraintViolation.getPropertyPath() + constraintViolation.getMessage())
        .collect(Collectors.toList());

        Map<String, List<String>> map = new HashMap<>();
        map.put("errors", errors);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(map);
        }

```
