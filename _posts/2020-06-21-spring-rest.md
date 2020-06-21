---
layout: post
title:  "Spring REST"
categories:  Spring REST
---

## RESTful 控制器
| 注解 | HTTP方法 | 使用场景 |
| --- | --- | --- |
| @GetMapping | HTTP GET 请求 | 读取资源数据 |
| @PostMapping | HTTP POST 请求 | 创建资源 |
| @PutMapping | HTTP PUT 请求 | 更新资源 |
| @PatchMapping | HTTP PATCH 请求 | 更新资源 | 
| @DeleteMapping | HTTP DELETE 请求| 删除资源 | 
| @RequestMapping | | 通用请求处理 |

```
@RestController
@RequestMapping(path="/design", produces="application/json")
@CrossOrigin(origins="*")
```
@RestController 注解
- 它是一个像 @Controller 和 @Service 这样的原型注解，它通过组件扫描来标记一个类。
- 告诉 Spring，控制器中的所有处理程序方法都应该将它们的返回值直接写入响应体。
@RequestMapping 注解
- 设置了一个 produces 属性。只在请求的 Accept 头包含 “application/json” 时才处理请求。
@CrossOrigin 注解
- 应用 CORS（跨源资源共享) ,允许来自任何域的客户端使用 API。

#### 获取
```
@GetMapping("/{id}")
public ResponseEntity<Taco> tacoById(@PathVariable("id") Long id) {
    Optional<Taco> optTaco = tacoRepo.findById(id);
    if (optTaco.isPresent()) {
        return new ResponseEntity<>(optTaco.get(), HttpStatus.OK);
    }
    
    return new ResponseEntity<>(null, HttpStatus.NOT_FOUND);
}
```
其中路径的 {id} 部分是占位符。请求中的实际值指定给 id 参数，该参数通过 @PathVariable 映射到 {id}占位符。

#### 创建
```
@PostMapping(consumes="application/json")
@ResponseStatus(HttpStatus.CREATED)
public Taco postTaco(@RequestBody Taco taco) {
    return tacoRepo.save(taco);
}
```
@PostMapping
- consumer 属性，表示该方法只处理 Content-type 与 application/json 匹配的请求。
@RequestBody
- 指示请求体应该转换为 Taco 对象并绑定到参数。
@ResponseStatus(HttpStatus.CREATED) 
- 在正常情况下（当没有抛出异常时），所有响应的 HTTP 状态码为 200（OK），表示请求成功。对于 POST 请求，HTTP 状态 201（CREATED）更具描述性，它告诉客户机，请求不仅成功了，而且还创建了一个资源。

#### 更新
虽然 PUT 经常用于更新资源数据，但它实际上是 GET 语义的对立面。
GET 请求用于将数据从服务器传输到客户机，而 PUT 请求用于将数据从客户机发送到服务器。
从这个意义上说，PUT 实际上是用于执行大规模替换操作，而不是更新操作。相反，HTTP PATCH 的目的是执行补丁或部分更新资源数据。
**PUT**
```
@PutMapping("/{orderId}")
public Order putOrder(@RequestBody Order order) {
    return repo.save(order);
}
```
要求客户端在 PUT 请求中提交完整的订单数据。
从语义上讲，PUT 的意思是“把这个数据放到这个 URL 上”，本质上是替换任何已经存在的数据。
**PATCH**
```
@PatchMapping(path="/{orderId}", consumes="application/json")
public Order patchOrder(@PathVariable("orderId") Long orderId, 
     @RequestBody Order patch) {
    
    Order order = repo.findById(orderId).get();
    
    if (patch.getDeliveryName() != null) {
        order.setDeliveryName(patch.getDeliveryName());
    }
    
    if (patch.getDeliveryStreet() != null) {
        order.setDeliveryStreet(patch.getDeliveryStreet());
    }
    
    if (patch.getDeliveryCity() != null) {
        order.setDeliveryCity(patch.getDeliveryCity());
    }
    
    if (patch.getDeliveryState() != null) {
        order.setDeliveryState(patch.getDeliveryState());
    }
    
    if (patch.getDeliveryZip() != null) {
        order.setDeliveryZip(patch.getDeliveryState());
    }
    
    if (patch.getCcNumber() != null) {
        order.setCcNumber(patch.getCcNumber());
    }
    
    if (patch.getCcExpiration() != null) {
        order.setCcExpiration(patch.getCcExpiration());
    }
    
    if (patch.getCcCVV() != null) {
        order.setCcCVV(patch.getCcCVV());
    }
    
    return repo.save(order);
}
```
不是用发送进来的新数据完全替换订单，而是检查传入订单对象的每个字段，并将任何非空值应用于现有订单。这种方法允许客户机只发送应该更改的属性，并允许服务器为客户机未指定的任何属性保留现有数据。

#### 删除
```
@DeleteMapping("/{orderId}")
@ResponseStatus(code=HttpStatus.NO_CONTENT)
public void deleteOrder(@PathVariable("orderId") Long orderId) {
    try {
        repo.deleteById(orderId);
    } catch (EmptyResultDataAccessException e) {}
}
```
@ResponseStatus 进行了注解，以确保响应的 HTTP 状态是 204（NO CONTENT）。
对于不再存在的资源，不需要将任何资源数据发送回客户机，因此对删除请求的响应通常没有正文，因此应该发送一个 HTTP 状态代码，让客户机知道不需要任何内容。

## 启用超媒体
超媒体作为应用程序状态的引擎（HATEOAS），是一种创建自描述 API 的方法，其中从 API 返回的资源包含到相关资源的链接。
API 提供的资源之间的关系，并在遍历这些关系时使用对这些关系的理解来发现 API 的 url。
```
{
    "_embedded": {
        "tacoResourceList": [
            {
                "name": "Veg-Out",
                "createdAt": "2018-01-31T20:15:53.219+0000",
                "ingredients": [
                    {
                        "name": "Flour Tortilla", "type": "WRAP",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/FLTO" }
                        }
                    },
                    {
                        "name": "Corn Tortilla", "type": "WRAP",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/COTO" }
                        }
                    },
                    {
                        "name": "Diced Tomatoes", "type": "VEGGIES",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/TMTO" }
                        }
                    },
                    {
                        "name": "Lettuce", "type": "VEGGIES",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/LETC" }
                        }
                    },
                    {
                        "name": "Salsa", "type": "SAUCE",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/SLSA" }
                        }
                    }
                ],
                "_links": {
                    "self": { "href": "http://localhost:8080/design/4" }
                }
            },
            ...
        ]
    },
    "_links": {
        "recents": {
            "href": "http://localhost:8080/design/recent"
        }
    }
}
```
这个新的 tacos 列表中的每个元素都包含一个名为 _links 的属性，该属性包含用于客户端引导的 API 超链接。
#### 添加依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```
#### 添加超链接
 Spring HATEOAS 提供了两种表示超链接资源的主要类型：
 - Resource 类型表示单个资源
 - Resources 是资源的集合
 ```
@GetMapping("/recent")
public Resources<Resource<Taco>> recentTacos() {
    PageRequest page = PageRequest.of(
        0, 12, Sort.by("createdAt").descending());
    
    List<Taco> tacos = tacoRepo.findAll(page).getContent();
    Resources<Resource<Taco>> recentResources = Resources.wrap(tacos);
    recentResources.add(
   recentResources.add(
        linkTo(methodOn(DesignTacoController.class).recentTacos())
        .withRel("recents"));
    
    return recentResources;
}
 ```
 #### 创建资源装配器
 ```
package tacos.web.api;
​
import java.util.Date;
import java.util.List;
import org.springframework.hateoas.ResourceSupport;
import lombok.Getter;
import tacos.Ingredient;
import tacos.Taco;
​
public class TacoResource extends ResourceSupport {
    
    @Getter
    private final String name;
    
    @Getter
    private final Date createdAt;
    
    @Getter
    private final List<Ingredient> ingredients;
    
    public TacoResource(Taco taco) {
        this.name = taco.getName();
        this.createdAt = taco.getCreatedAt();
        this.ingredients = taco.getIngredients();
    }
}
 ```
 TacoResource 不包含 Taco 的 id 属性。这是因为不需要在 API 中公开任何特定于数据库的 id。
 从 API 客户机的角度来看，资源的自链接将作为资源的标识符。
 TacoResource 只有一个构造函数，它接受一个 Taco 并将相关属性从 Taco 复制到自己的属性。
```
package tacos.web.api;
​
import org.springframework.hateoas.mvc.ResourceAssemblerSupport;
import tacos.Taco;
​
public class TacoResourceAssembler extends ResourceAssemblerSupport<Taco, TacoResource> {
    
    public TacoResourceAssembler() {
        super(DesignTacoController.class, TacoResource.class);
    }
    
    @Override
    protected TacoResource instantiateResource(Taco taco) {
        return new TacoResource(taco);
    }
    
    @Override
    public TacoResource toResource(Taco taco) {
        return createResourceWithId(taco.getId(), taco);
    }
}
```
```
@GetMapping("/recent")
public Resources<TacoResource> recentTacos() {
    PageRequest page = PageRequest.of(
        0, 12, Sort.by("createdAt").descending());
    
    List<Taco> tacos = tacoRepo.findAll(page).getContent();
    List<TacoResource> tacoResources = new TacoResourceAssembler().toResources(tacos);
    
    Resources<TacoResource> recentResources = new Resources<TacoResource>(tacoResources);
    recentResources.add(
        linkTo(methodOn(DesignTacoController.class).recentTacos())
        .withRel("recents"));
    
    return recentResources;
}
```
```
@Relation(value="taco", collectionRelation="tacos")
public class TacoResources extends ResourcesSupport {
    ...
}
```
@Relation 注解可以帮助打破 JSON 字段名与 Java 中定义的资源类型类名之间的耦合。可以指定 Spring HATEOAS 应该如何在 JSON 结果中字段的命名.

## 启用以数据为中心的服务
Spring Data REST 是 Spring Data 家族中的另一个成员，它为 Spring Data 创建的存储库自动创建REST API。
只需将 Spring Data REST 添加到构建中，就可以获得一个 API，其中包含所定义的每个存储库接口的操作。
#### 添加依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
```
#### 基础设置
设置 spring.data.rest 基本路径属性
```
spring:
  data:
    rest:
      base-path: /api

```
#### 调整资源路径和关系名称
Spring Data REST 公开了一个 home 资源，其中包含所有公开端点的链接。只需向 API 基础路径发出 GET 请求即可获得：
```
$ curl localhost:8080/api
{
    "_links" : {
        "orders" : {
            "href" : "http://localhost:8080/api/orders"
        },
        "ingredients" : {
            "href" : "http://localhost:8080/api/ingredients"
        },
        "tacoes" : {
            "href" : "http://localhost:8080/api/tacoes{?page,size,sort}",
            "templated" : true
        },
        "users" : {
            "href" : "http://localhost:8080/api/users"
        },
        "profile" : {
            "href" : "http://localhost:8080/api/profile"
        }
    }
}
```

```
@Data
@Entity
@RestResource(rel="tacos", path="tacos")
public class Taco {
    ...
}
```

#### 分页和排序
```
$ curl "localhost:8080/api/tacos?sort=createAt,desc?page=0&size=12"
```

#### 添加用户端点
```
package tacos.web.api;
​
import static org.springframework.hateoas.mvc.ControllerLinkBuilder.*;
​
import java.util.List;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.data.rest.webmvc.RepositoryRestController;
import org.springframework.hateoas.Resources;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
​
import tacos.Taco;
import tacos.data.TacoRepository;
​
@RepositoryRestController
public class RecentTacosController {
    
    private TacoRepository tacoRepo;
    
    public RecentTacosController(TacoRepository tacoRepo) {
        this.tacoRepo = tacoRepo;
    }
    
    @GetMapping(path="/tacos/recent", produces="application/hal+json")
    public ResponseEntity<Resources<TacoResource>> recentTacos() {
        PageRequest page = PageRequest.of(
            0, 12, Sort.by("createdAt").descending());
        List<Taco> tacos = tacoRepo.findAll(page).getContent();
    
        List<TacoResource> tacoResources =
            new TacoResourceAssembler().toResources(tacos);
        Resources<TacoResource> recentResources =
            new Resources<TacoResource>(tacoResources);
        
        recentResources.add(
            linkTo(methodOn(RecentTacosController.class).recentTacos())
            .withRel("recents"));
        
        return new ResponseEntity<>(recentResources, HttpStatus.OK);
    }
}
```
@RepositoryRestController 注解将确保它以 Spring Data REST 的基本路径作为前缀。

#### 向 Spring Data 端点添加用户超链接
Spring Data HATEOAS 提供了 ResourceProcessor，这是一个在通过 API 返回资源之前操作资源的接口。
```
@Bean
public ResourceProcessor<PagedResources<Resource<Taco>>>
    tacoProcessor(EntityLinks links) {
    
    return new ResourceProcessor<PagedResources<Resource<Taco>>>() {
        @Override
        public PagedResources<Resource<Taco>> process(
            PagedResources<Resource<Taco>> resource) {
            resource.add(
                links.linkFor(Taco.class)
                .slash("recent")
                .withRel("recents"));
            
            return resource;
        }
    };
}
```