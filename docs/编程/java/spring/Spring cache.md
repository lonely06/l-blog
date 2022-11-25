
# 1. 简介

-  **Spring Cache**是一个框架，实现了基于注解的缓存功能。 
-  Spring Cache只是提供了一层抽象，底层可以切换不同的cache实现。具体就是通过**CacheManager**接口来统一不同的缓存技术。CacheManager是Spring提供的各种缓存技术抽象接口。 
-  针对不同的缓存技术需要实现不同的CacheManager：  
| **CacheManager** | **描述** |
| --- | --- |
| EhCacheCacheManager | 使用EhCache作为缓存技术 |
| GuavaCacheManager | 使用Google的GuavaCache作为缓存技术 |
| RedisCacheManager | 使用Redis作为缓存技术 |



# 2. 注解
| **注解** | **说明** |
| --- | --- |
| [@EnableCaching ](/EnableCaching ) | 开启缓存注解功能 |
| [@Cacheable ](/Cacheable ) | 在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中 |
| [@CachePut ](/CachePut ) | 将方法的返回值放到缓存中 |
| [@CacheEvict ](/CacheEvict ) | 将一条或多条数据从缓存中删除 |



# 3. 使用


## 3.1 @CachePut注解

- 作用: 将方法返回值，放入缓存
- value: 缓存的名称, 每个缓存名称下面可以有很多key
- key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法
```java
@CachePut(value = "userCache", key = "#user.id")
@PostMapping
public User save(User user){
    userService.save(user);
    return user;
}
```


> key的写法如下：
>  
> 	#user.id : #user指的是方法形参的名称, id指的是user的id属性 , 也就是使用user的id属性作为key ;
>  
> 	#user.name: #user指的是方法形参的名称, name指的是user的name属性 ,也就是使用user的name属性作为key ;
>  
> 
>  
> 	#result.id : #result代表方法返回值，该表达式 代表以返回对象的id属性作为key ；
>  
> 	#result.name : #result代表方法返回值，该表达式 代表以返回对象的name属性作为key ；



## 3.2 @CacheEvict注解

- 作用: 清理指定缓存
- value: 缓存的名称，每个缓存名称下面可以有多个key
- key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法

```java
//删除操作
@CacheEvict(value = "userCache",key = "#p0")  //#p0 代表第一个参数
//@CacheEvict(value = "userCache",key = "#root.args[0]") //#root.args[0] 代表第一个参数
//@CacheEvict(value = "userCache",key = "#id") //#id 代表变量名为id的参数
@DeleteMapping("/{id}")
public void delete(@PathVariable Long id){
    userService.removeById(id);
}

//更新操作
//@CacheEvict(value = "userCache",key = "#p0.id")   //第一个参数的id属性
//@CacheEvict(value = "userCache",key = "#user.id") //参数名为user参数的id属性
//@CacheEvict(value = "userCache",key = "#root.args[0].id") //第一个参数的id属性
@CacheEvict(value = "userCache",key = "#result.id")         //返回值的id属性
@PutMapping
public User update(User user){
    userService.updateById(user);
    return user;
}
```


## 3.3 @Cacheable注解

- 作用: 在方法执行前，spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中
- value: 缓存的名称，每个缓存名称下面可以有多个key
- key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法

```java
@Cacheable(value = "userCache",key = "#id")
@GetMapping("/{id}")
public User getById(@PathVariable Long id){
    User user = userService.getById(id);
    return user;
}
```

-  缓存非null值 
   - conditio: 表示满足什么条件，再进行缓存
   - unless: 表示满足条件则不缓存；与上述的condition是反向的
```java
@Cacheable(value = "userCache",key = "#id", unless = "#result == null")
@GetMapping("/{id}")
public User getById(@PathVariable Long id){
    User user = userService.getById(id);
    return user;
}
/*
	注意：
		此处，使用的时候只能够使用unless，因为在condition中，无法获取到结果#result
*/
```


# 4. 集成redis

-  pom.xml 
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
 

-  application.yml 
```yaml
spring:
  redis:
    host: 192.168.200.200
    port: 6379
    password: root@123456
    database: 0
  cache:
    redis:
      time-to-live: 1800000   #设置缓存过期时间，可选
```
 
