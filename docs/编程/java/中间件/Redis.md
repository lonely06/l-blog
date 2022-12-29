---
title: Redis
---
## 1. 简介

-  Redis中文网：[https://www.redis.net.cn](https://www.redis.net.cn) 
-  共5种常用数据类型 
   -  字符串(string)：普通字符串，常用 
   -  哈希(hash)：string类型的 field 和 value 的映射表, 适合存储对象 
   -  列表(list)：按照插入顺序排序，可以有重复元素 
   -  集合(set)：无序集合，没有重复元素 
   -  有序集合(sorted set / zset)：集合中每个元素关联一个double类型的分数（score），根据分数升序排序，没有重复元素，分数可以重复 

![](http://img.lonely.icu/lonely-md/202211291405701.png)


## 2. 常用命令


### 2.1 字符串string

-  设置指定key的值<br />`set key value` 
-  获取指定key的值<br />`get key` 
-  设置指定key的值，并设定过期时间为second<br />`setex key seconds value` 
-  key不存在时设置key的值<br />`setnx key value` 


### 2.2 哈希hash

-  将哈希表key中的字段field的值设置为value<br />`hset key field value` 
-  获取存储在哈希表中指定字段的值<br />`hget key field` 
-  删除存储在哈希表中的指定字段<br />`hdel key field` 
-  获取哈希表中所有字段<br />`hkeys key` 
-  获取哈希表中所有值<br />`hvals key` 
-  获取在哈希表中指定key的所有字段和值<br />`hgetall key` 

![](http://img.lonely.icu/lonely-md/202210172101504.png)


### 2.3 列表list

-  将一个或多个值插入到列表头部<br />`lpush key value1 [value2]` 
-  获取列表指定范围内的元素<br />`lrange key start stop` 
-  移除并获取列表最后一个元素<br />`rpop key` 
-  获取列表长度<br />`llen key` 
-  出并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止<br />`brpop key1 [key2] timeout` 

![](http://img.lonely.icu/lonely-md/202210172104027.png)


### 2.4 集合set

-  向集合添加一个或多个成员<br />`sadd key member1 [member2]` 
-  返回集合中的所有成员<br />`smembers key` 
-  获取集合的成员数<br />`scard key` 
-  返回给定所有集合的交集<br />`sinter key1 [key2]` 
-  返回所有给定集合的并集<br />`sunion key1 [key2]` 
-  返回给定所有集合的差集<br />`sdiff key1 [key2]` 
-  移除集合中一个或多个成员<br />`srem key member1 [member2]` 

![](http://img.lonely.icu/lonely-md/202210172107325.png)


### 2.5 有序集合sorted set

-  向有序集合添加一个或多个成员，或者更新已存在成员的分数<br />`zadd key score1 member1 [score2 member2]` 
-  过索引区间返回有序集合中指定区间内的成员<br />`zrange key start stop [withscores]` 
-  有序集合中对指定成员的分数加上增量 increment<br />`zincrby key increment member` 
-  移除有序集合中的一个或多个成员<br />`zrem key member [member...]` 


### 2.6 通用命令

-  查找所有符合给定模式(pattern)的key<br />`keys pattern` 
-  检查给定key是否存在<br />`exists key` 
-  返回key所储存的值的类型<br />`type key` 
-  返回给定key的剩余生存时间(TTL, time to live)，以秒为单位<br />`ttl key` 
-  在key存在时删除key<br />`del key` 


## 3. 在java中使用


### 3.1 jedis

-  maven坐标 
```xml
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>2.8.0</version>
</dependency>
```


-  步骤 
   1. 获取连接
   2. 执行操作
   3. 关闭连接
```java
import org.junit.Test;
import redis.clients.jedis.Jedis;
import java.util.Set;

/**
 * 使用Jedis操作Redis
 */
public class JedisTest {
    @Test
    public void testRedis(){
        //1 获取连接
        Jedis jedis = new Jedis("localhost",6379);
        
        //2 执行具体的操作
        jedis.set("username","xiaoming");

        String value = jedis.get("username");
        System.out.println(value);

        jedis.hset("myhash","addr","bj");
        String hValue = jedis.hget("myhash", "addr");
        System.out.println(hValue);

        Set<String> keys = jedis.keys("*");
        for (String key : keys) {
            System.out.println(key);
        }
        
        //3 关闭连接
        jedis.close();
    }
}
```

-  工具类 
```java
public class JedisPoolUtils {
	private static JedisPool jedisPool;

	static{
		//读取配置文件
		InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
		//创建Properties对象
		Properties pro = new Properties();
		//关联文件
		try {
			pro.load(is);
		} catch (IOException e) {
			e.printStackTrace();
		}
		//获取数据，设置到JedisPoolConfig中
		JedisPoolConfig config = new JedisPoolConfig();
		config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
		config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));

		//初始化JedisPool
		jedisPool = new JedisPool(config,pro.getProperty("host"),Integer.parseInt(pro.getProperty("port")));	
	}
	
	/**
	 * 获取连接方法
	 */
	public static Jedis getJedis(){
		return jedisPool.getResource();
	}
}
```



### 3.2 spring data redis

-  maven坐标 
```xml
<dependency>
	<groupId>org.springframework.data</groupId>
	<artifactId>spring-data-redis</artifactId>
	<version>2.4.8</version>
</dependency>
```


-  springboot中maven坐标 
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```


-  spring data redis中进行了封装：RedisTemplate 
   - ValueOperations：简单K-V操作
   - SetOperations：set类型数据操作
   - ZSetOperations：zset类型数据操作
   - HashOperations：针对hash类型的数据操作
   - ListOperations：针对list类型的数据操作


#### springboot中使用


##### 1. 配置xml

```yaml
spring:
  死死死死1Redis相关配置
  redis:
    host: localhost
    port: 6379
    死死死死1password: 123456
    database: 0 死死死死1操作的是0号数据库
    jedis:
      死死死死1Redis连接池配置
      pool:
        max-active: 8 死死死死1最大连接数
        max-wait: 1ms 死死死死1连接池最大阻塞等待时间
        max-idle: 4 死死死死1连接池中的最大空闲连接
        min-idle: 0 死死死死1连接池中的最小空闲连接
```


##### 2. 提供配置类

- Spring Boot框架会自动装配RedisTemplate对象，默认的key序列化器为JdkSerializationRedisSerializer，导致存到Redis中的数据和原始数据有差别

```java
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * Redis配置类
 */
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {

        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();

        //默认的Key序列化器为：JdkSerializationRedisSerializer
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());

        redisTemplate.setConnectionFactory(connectionFactory);

        return redisTemplate;
    }
}
```


##### 3. 操作字符串类型

```java
public void testString(){
    //存值
    redisTemplate.opsForValue().set("city123","beijing");

    //取值
    String value = (String) redisTemplate.opsForValue().get("city123");
    System.out.println(value);

    //存值，同时设置过期时间
    redisTemplate.opsForValue().set("key1","value1",10l, TimeUnit.SECONDS);

    //存值，如果存在则不执行任何操作
    Boolean aBoolean = redisTemplate.opsForValue().setIfAbsent("city1234", "nanjing");
    System.out.println(aBoolean);
}
```


##### 4. 操作哈希类型

```java
public void testHash(){
    HashOperations hashOperations = redisTemplate.opsForHash();

    //存值
    hashOperations.put("002","name","xiaoming");
    hashOperations.put("002","age","20");
    hashOperations.put("002","address","bj");

    //取值
    String age = (String) hashOperations.get("002", "age");
    System.out.println(age);

    //获得hash结构中的所有字段
    Set keys = hashOperations.keys("002");
    for (Object key : keys) {
        System.out.println(key);
    }

    //获得hash结构中的所有值
    List values = hashOperations.values("002");
    for (Object value : values) {
        System.out.println(value);
    }
}
```


##### 5. 操作列表类型

```java
public void testList(){
    ListOperations listOperations = redisTemplate.opsForList();

    //存值
    listOperations.leftPush("mylist","a");
    listOperations.leftPushAll("mylist","b","c","d");

    //取值
    List<String> mylist = listOperations.range("mylist", 0, -1);
    for (String value : mylist) {
        System.out.println(value);
    }

    //获得列表长度 llen
    Long size = listOperations.size("mylist");
    int lSize = size.intValue();
    for (int i = 0; i < lSize; i++) {
        //出队列
        String element = (String) listOperations.rightPop("mylist");
        System.out.println(element);
    }
}
```


##### 6. 操作集合类型

```java
public void testSet(){
    SetOperations setOperations = redisTemplate.opsForSet();

    //存值
    setOperations.add("myset","a","b","c","a");

    //取值
    Set<String> myset = setOperations.members("myset");
    for (String o : myset) {
        System.out.println(o);
    }

    //删除成员
    setOperations.remove("myset","a","b");

    //取值
    myset = setOperations.members("myset");
    for (String o : myset) {
        System.out.println(o);
    }
}
```


##### 7. 操作有序集合类型

```java
public void testZset(){
    ZSetOperations zSetOperations = redisTemplate.opsForZSet();

    //存值
    zSetOperations.add("myZset","a",10.0);
    zSetOperations.add("myZset","b",11.0);
    zSetOperations.add("myZset","c",12.0);
    zSetOperations.add("myZset","a",13.0);

    //取值
    Set<String> myZset = zSetOperations.range("myZset", 0, -1);
    for (String s : myZset) {
        System.out.println(s);
    }

    //修改分数
    zSetOperations.incrementScore("myZset","b",20.0);

    //取值
    myZset = zSetOperations.range("myZset", 0, -1);
    for (String s : myZset) {
        System.out.println(s);
    }

    //删除成员
    zSetOperations.remove("myZset","a","b");

    //取值
    myZset = zSetOperations.range("myZset", 0, -1);
    for (String s : myZset) {
        System.out.println(s);
    }
}
```


##### 8. 通用操作

```java
public void testCommon(){
    //获取Redis中所有的key
    Set<String> keys = redisTemplate.keys("*");
    for (String key : keys) {
        System.out.println(key);
    }

    //判断某个key是否存在
    Boolean itcast = redisTemplate.hasKey("itcast");
    System.out.println(itcast);

    //删除指定key
    redisTemplate.delete("myZset");

    //获取指定key对应的value的数据类型
    DataType dataType = redisTemplate.type("myset");
    System.out.println(dataType.name());
}
```

## 4. 持久化

### 4.1 概念

- redis是一个内存数据库，当redis服务器重启或电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中

### 4.2 持久化机制

1.  RDB：默认方式，不需要进行配置，默认就使用这种机制 
   -  在一定的间隔时间中，检测key的变化情况，然后持久化数据 
   -  编辑redis.conf文件 
```properties
死死死死1after 900 sec (15 min) if at least 1 key changed
save 900 1
死死死死1after 300 sec (5 min) if at least 10 keys changed
save 300 10
死死死死1after 60 sec if at least 10000 keys changed
save 60 10000
```


2.  AOF：日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后，持久化数据 
   -  编辑redis.conf文件 
```properties
appendonly no（关闭aof） --> appendonly yes （开启aof）
## appendfsync always ： 每一次操作都进行持久化
appendfsync everysec ： 每隔一秒进行一次持久化
## appendfsync no	 ： 不进行持久化
```

