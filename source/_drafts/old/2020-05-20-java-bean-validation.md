---
layout: post
title:  "JSR 303 - Java Bean Validation"
categories:  JSR-303 validation
---

## 规范
(JSR-303)[https://jcp.org/en/jsr/detail?id=303]

## 依赖
```
implementation 'javax.validation:validation-api'
implementation 'org.hibernate.validator:hibernate-validator'
```

## 注解
#### 空检查
@Null 被注释的元素必须为 null (任何类型)
@NotNull 被注释的元素必须不为 null (任何类型)
#### 布尔检查
@AssertTrue 被注释的元素必须为 true (boolean或者Boolean)
@AssertFalse 被注释的元素必须为 false (boolean或者Boolean)
#### 数值检查
@Min(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 (BigDecimal,BigInteger,byte,short,int,long及其包装类)
@Max(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Range 被注释的元素必须在合适的范围内 (内部使用@Min和@Max实现)
@DecimalMin(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 (CharSequence，BigDecimal,BigInteger,byte,short,int,long及其包装类)
@DecimalMax(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Digits (integer, fraction) 被注释的元素必须是一个数字，其值必须在可接受的范围内 (BigDecimal,BigInteger,byte,short,int,long及其包装类)
#### 字符串（集合）检查
@Size(max, min) 被注释的元素的大小必须在指定的范围内 (CharSequence,Collection,Map,Array)
@Pattern(value) 被注释的元素必须符合指定的正则表达式 (CharSequence)
@NotEmpty 被注释的字符串的必须非空
@NotBlank 被注释的字符串的必须非空白
@Length 被注释的字符串的大小必须在指定的范围内（同@Size）
#### 时间检查
@Past 被注释的元素必须是一个过去的日期 (Date,Calendar)
@Future 被注释的元素必须是一个将来的日期 (Date,Calendar)
Hibernate Validator扩展的constraint
Hibernate除了实现标准的constraint，还实现了一些扩展constraint。
#### 特定格式检查
@URL 被注释的字符串必须是合法的URL
@Email 被注释的元素必须是电子邮箱地址
@SafeHtml 被注解的字符串必须是合法的HTML
@CreditCardNumber 被注释的元素必须是合法的信用卡号，使用的是Luhn算法
#### 指定脚本
@ScriptAssert 直接指定脚本进行校验，算是最灵活的了

