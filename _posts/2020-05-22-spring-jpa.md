---
layout: post
title:  "spring JPA"
categories:  spring JPA
---

## 引入依赖
```
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
```

## 注册域为实体
```
@Data
@RequiredArgsConstructor
@NoArgsConstructor(access=AccessLevel.PRIVATE, force=true)
@Entity
public class Ingredient {
    @Id
    private final String id;
    private final String name;
    private final Type type;
    
    public static enum Type {
        WRAP, PROTEIN, VEGGIES, CHEESE, SAUCE
    }
}
```
注解：
- @Entity，将其声明为 JPA 实体。
- @NoArgsConstructor: 自动生成无参数构造函数。JPA 要求实体有一个无参构造函数。
- @AllArgsConstructor: 自动生成全参数构造函数。
- @RequiredArgsConstructor。@Data 隐式地添加了一个必需的有参构造函数，但是当使用 @NoArgsConstructor 时，该构造函数将被删除。显式的 @RequiredArgsConstructor 确保除了私有无参数构造函数外，仍然有一个必需有参构造函数。
- @Id，id 属性必须使用 @Id 进行注解，以便将其指定为惟一标识数据库中实体的属性。

```
@Data
@Entity
public class Taco {
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    @NotNull
    @Size(min=5, message="Name must be at least 5 characters long")
    private String name;
    
    private Date createdAt;
    
    @ManyToMany(targetEntity=Ingredient.class)
    @Size(min=1, message="You must choose at least 1 ingredient")
    private List<Ingredient> ingredients;
    
    @PrePersist
    void createdAt() {
        this.createdAt = new Date();
    }
}
```
- @ManyToMany, 表明多对多的关系，一个 Taco 可以有很多 Ingredient，一个 Ingredient 也可以对应多个 Taco。
- @PrePersist，生命周期中的一个回调方法。它将 createdAt 属性设置为保存 Taco 之前的当前日期和时间。
```
@Data
@Entity
@Table(name="Taco_Order")
public class Order implements Serializable {
    private static final long serialVersionUID = 1L;
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    private Date placedAt;
    
    ...
    
    @ManyToMany(targetEntity=Taco.class)
    private List<Taco> tacos = new ArrayList<>();
    
    public void addDesign(Taco design) {
        this.tacos.add(design);
    }
    
    @PrePersist
    void placedAt() {
        this.placedAt = new Date();
    }
}
```
- @Table, 指定数据库中指定表名。默认为对应类名。

## 声明 JPA repository
```
public interface IngredientRepository extends CrudRepository<Ingredient, String> {
}
```
CrudRepository 为 CRUD（创建、读取、更新、删除）操作声明了十几个方法。
- 第一个参数是存储库要持久化的实体类型
- 第二个参数是实体 id 属性的类型
不需要编写那十几个方法的实现，当应用程序启动时，Spring Data JPA 会动态地自动生成实现。

## 自定义 JPA repository
```
List<Order> findByDeliveryZip(String deliveryZip);
```
Spring Data 检查存储库接口中的方法，解析方法名称，并尝试在持久化对象的上下文中理解方法的用途（在本例中是 Order）。本质上，Spring Data 定义了一种小型的领域特定语言（DSL），其中持久化细节用 repository 中的方法签名表示。  
Spring Data 知道这个方法是用来查找订单的，因为已经用 Order 参数化了 CrudRepository。方法名 findByDeliveryZip() 表明，该方法应该通过将其 deliveryZip 属性与作为参数，传递给匹配的方法来查找所有订单实体。  
repository 的方法由一个动词、一个可选的主语、单词 by 和一个谓词组成。在 findByDeliveryZip() 中，动词是 find，谓词是 DeliveryZip，主语没有指定，暗示是一个 Order。
Spring Data 方法签名还可以包括以下任何操作：
- IsAfter, After, IsGreaterThan, GreaterThan
- IsGreaterThanEqual, GreaterThanEqual
- IsBefore, Before, IsLessThan, LessThan
- IsLessThanEqual, LessThanEqual
- IsBetween, Between
- IsNull, Null
- IsNotNull, NotNull
- IsIn, In
- IsNotIn, NotIn
- IsStartingWith, StartingWith, StartsWith
- IsEndingWith, EndingWith, EndsWith
- IsContaining, Containing, Contains
- IsLike, Like
- IsNotLike, NotLike
- IsTrue, True
- IsFalse, False
- Is, Equals
- IsNot, Not
- IgnoringCase, IgnoresCase

#### 自定义查询
```
@Query("Order o where o.deliveryCity='Seattle'")
List<Order> readOrdersDeliveredInSeattle();
```
可以随意将方法命名为任何想要的名称，并使用 @Query 对其进行注解，以显式地指定调用方法时要执行的查询。

## 初始化数据库
Spring Boot提供两种方法来定义数据库的表结构以及添加数据。
- 使用Hibernate提供的工具来创建表结构，该机制会自动搜索@Entity实体对象并创建对应的表，然后使用import.sql文件导入测试数据；
- 利用旧的Spring JDBC，通过schema.sql文件定义数据库的表结构、通过data.sql导入测试数据。
JPA有个生成DDL的特性，并且可以设置为在数据库启动时运行，这可以通过两个外部属性进行控制：
- spring.jpa.generate-ddl （ boolean ）控制该特性的关闭和开启，跟实现者没关系。
- spring.jpa.hibernate.ddl-auto （ enum ）是一个Hibernate特性，用于更细力度的控制该行为。
