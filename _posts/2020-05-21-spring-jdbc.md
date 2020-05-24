---
layout: post
title:  "spring JDBC"
categories:  spring JDBC
---

## 添加依赖
```
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
implementation 'com.h2database:h2'
```
#### h2 数据库
h2是一个开源的嵌入式数据库引擎，采用java语言编写。
h2还提供兼容模式，可以兼容一些主流的数据库，因此采用H2作为开发期的数据库非常方便。

## 定义JDBC 存储库
Ingredient repository 需要执行以下操作：
- 查询所有的 Ingredient 使之变成一个 Ingredient 的集合对象
- 通过 id 查询单个 Ingredient
- 保存一个 Ingredient 对象
```
public interface IngredientRepository {

    Iterable<Ingredient> findAll();

    Ingredient findOne(String id);

    Ingredient save(Ingredient ingredient);
}
```
该接口体现了需要 Ingredient repository 做的事情的本质。
接下来需要编写一个使用 JdbcTemplate 来查询数据库的 IngredientRepository 的实现。
```
@Repository
public class JdbcIngredientRepository implements IngredientRepository {
    
    private JdbcTemplate jdbc;
    
    @Autowired
    public JdbcIngredientRepository(JdbcTemplate jdbc) {
        this.jdbc = jdbc;
    }
    
    // TODO
​
}
```
使用 @Repository 进行注解，会由 Spring 组件在扫描时自动发现，并在 Spring 应用程序上下文中生成 bean 实例。
当 Spring 创建 JdbcIngredientRepository bean 时，通过 @Autowired 注解将 JdbcTemplate 注入到 bean 中。
构造函数将 JdbcTemplate 分配给一个实例变量，该变量将在其他方法中用于查询和插入数据库。

## 使用 JdbcTemplate 查询数据库
```
@Override
public Iterable<Ingredient> findAll() {
    return jdbc.query("select id, name, type from Ingredient",
              this::mapRowToIngredient);
}
​
@Override
public Ingredient findOne(String id) {
    return jdbc.queryForObject(
        "select id, name, type from Ingredient where id=?",
        this::mapRowToIngredient, id);
}
​
private Ingredient mapRowToIngredient(ResultSet rs, int rowNum)
    throws SQLException {
    return new Ingredient(
        rs.getString("id"),
        rs.getString("name"),
        Ingredient.Type.valueOf(rs.getString("type")));
}
```
使用 Java 8 的 lambda
```
@Override
public Ingredient findOne(String id) {
    return jdbc.queryForObject(
        "select id, name, type from Ingredient where id=?",
        new RowMapper<Ingredient>() {
            public Ingredient mapRow(ResultSet rs, int rowNum)
                throws SQLException {
                return new Ingredient(
                    rs.getString("id"),
                    rs.getString("name"),
                    Ingredient.Type.valueOf(rs.getString("type")));
            };
        }, id);
}
```

## 使用 JdbcTemplate 插入数据
```
@Override
public Ingredient save(Ingredient ingredient) {
    jdbc.update(
        "insert into Ingredient (id, name, type) values (?, ?, ?)",
        ingredient.getId(),
        ingredient.getName(),
        ingredient.getType().toString());
    return ingredient;
}
```
#### 插入数据时获取id
```
private long saveTacoInfo(Taco taco) {
        taco.setCreatedAt(new Date());
        PreparedStatementCreator psc = new PreparedStatementCreatorFactory(
            "insert into Taco (name, createdAt) values (?, ?)",
            Types.VARCHAR, Types.TIMESTAMP
        ).newPreparedStatementCreator(
            Arrays.asList(
                taco.getName(),
                new Timestamp(taco.getCreatedAt().getTime())));
        
        KeyHolder keyHolder = new GeneratedKeyHolder();
        jdbc.update(psc, keyHolder);
        return keyHolder.getKey().longValue();
    }

```
需要的 update() 方法接受 PreparedStatementCreator 和 KeyHolder。可以通过 keyHolder.getKey().longValue() 来返回 Taco id。

## 定义 shame
定义一个名为 schema.sql 的文件。然后放在项目的 src/main/resources 文件夹下。  
在应用程序的类路径根目录下执行 sql，然后在应用程序启动时对数据库执行该文件中的 SQL。

## 预加载数据
Spring Boot 还将执行一个名为 data.sql 的文件，这个文件位于src/main/resources/下。

