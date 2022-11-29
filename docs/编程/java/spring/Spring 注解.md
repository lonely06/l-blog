---
title: Spring 注解
---
# 1 IOC/DI注解开发


## 1.1 注解开发定义bean

1. 删除原XML配置中的`

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
```

2. 在对应类上添加注解

```java
@Component("bookDao")
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ..." );
    }
}
```

3. 配置Spring的注解包扫描 
   - 为了让Spring框架能够扫描到写在类上的注解，需要在配置文件上进行包扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <context:component-scan base-package="com.itheima"/>
</beans>
```

-  注意： 
   - @Component注解如果不起名称，将按照类型获取bean，或按照名称获取，会有一个默认值`当前类名首字母小写`，如
```java
BookService bookService = (BookService)ctx.getBean("bookServiceImpl");
System.out.println(bookService);
```

-  对于@Component注解，还衍生出了其他三个注解`@Controller`、`@Service`、`@Repository`分别对应`表现层`、`业务层`、`数据层` 


## 1.2 纯注解开发模式

1. 创建配置类

```java
public class SpringConfig {
}
```

2. 在配置类上添加`@Configuration`注解，将其标识为一个配置类,替换`applicationContext.xml`

```java
@Configuration
public class SpringConfig {
}
```

3. 在配置类上添加包扫描注解`@ComponentScan`替换`<context:component-scan base-package=""/>`

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfig {
}
```

- @Configuration注解用于设定当前类为配置类
- @ComponentScan注解用于设定扫描路径，此注解只能添加一次，多个数据用数组格式
```
@ComponentScan({com.itheima.service","com.itheima.dao"})
```


- 读取Spring核心配置文件初始化容器对象切换为读取Java配置类初始化容器对象
```java
//加载配置文件初始化容器
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
//加载配置类初始化容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```



## 1.3 注解开发bean作用范围与生命周期管理


### 1.3.1 作用范围  [@scope ](/scope ) 

- 在其类上添加`@scope`注解

```java
@Repository
//@Scope设置bean的作用范围  默认值singleton（单例），可选值prototype（非单例）
@Scope("prototype")
public class BookDaoImpl implements BookDao {

    public void save() {
        System.out.println("book dao save ...");
    }
}
```


### 1.3.2 生命周期  @PostConstruct和[@PreDestroy ](/PreDestroy ) 

- 添加2个方法，在对应的方法上添加`@PostConstruct`和`@PreDestroy`注解

```java
@Repository
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }
    @PostConstruct //在构造方法之后执行，替换 init-method
    public void init() {
        System.out.println("init ...");
    }
    @PreDestroy //在销毁方法之前执行,替换 destroy-method
    public void destroy() {
        System.out.println("destroy ...");
    }
}
```

- 注意:`@PostConstruct`和`@PreDestroy`注解如果找不到，需要导入下面的jar包
```java
<dependency>
  <groupId>javax.annotation</groupId>
  <artifactId>javax.annotation-api</artifactId>
  <version>1.3.2</version>
</dependency>
```


   - 原因：从JDK9以后jdk中的javax.annotation包被移除了，这两个注解刚好就在这个包中。


## 1.4 注解开发依赖注入

- Spring为了使用注解简化开发，并没有提供`构造函数注入`、`setter注入`对应的注解，只提供了自动装配的注解实现。


### 1.4.2 按照类型注入  [@Autowired ](/Autowired ) 

- 在类的属性上添加`@Autowired`注解，去除setter方法

```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookDao bookDao;
   
    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```


### 1.4.3 按名称注入  [@Qualifier ](/Qualifier ) 

- 使用`@Qualifier`来指定注入哪个名称的bean对象。
- @Qualifier不能独立使用，必须和@Autowired一起使用

```java
@Service
public class BookServiceImpl implements BookService {
    @Autowired
    @Qualifier("bookDao1")
    private BookDao bookDao;
    
    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```


### 1.4.4 简单数据类型注入  [@Value ](/Value ) 

- 使用`@Value`注解，将值写入注解的参数中

```java
@Repository("bookDao")
public class BookDaoImpl implements BookDao {
    @Value("itheima")
    private String name;
    public void save() {
        System.out.println("book dao save ..." + name);
    }
}
```


### 1.4.5 注解读取properties配置文件  [@PropertySource ](/PropertySource ) 

1. resource下准备properties文件
2. 在配置类上添加`@PropertySource`注解，加载properties配置文件

```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("jdbc.properties")
public class SpringConfig {
}
```

3. 使用@Value读取配置文件中的内容

```java
@Repository("bookDao")
public class BookDaoImpl implements BookDao {
    @Value("${name}")
    private String name;
    public void save() {
        System.out.println("book dao save ..." + name);
    }
}
```

- 注意: 
   - 如果读取的properties配置文件有多个，可以使用`@PropertySource`的属性来指定多个
```java
@PropertySource({"jdbc.properties","xxx.properties"})
```


   - `@PropertySource`注解属性中不支持使用通配符`*`
```java
@PropertySource({"*.properties"})
```


   - `@PropertySource`注解属性中可以把`classpath:`加上,代表从当前项目的根路径找文件
```java
@PropertySource({"classpath:jdbc.properties"})
```



# 2 IOC/DI注解开发管理第三方bean


### 2.1 注解开发管理第三方bean

- 在方法上添加@Bean注解。@Bean注解的作用是将方法的返回值制作为Spring管理的一个bean对象


### 2.2 引入外部配置类

1. 使用包扫描引入(不推荐) 
   - 在类上添加@Configuration注解
2. 使用`@Import`引入 
   1. 去除JdbcConfig类上的注解
```java
public class JdbcConfig {
	@Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("com.mysql.jdbc.Driver");
        ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
        ds.setUsername("root");
        ds.setPassword("root");
        return ds;
    }
}
```

   2. 在Spring配置类中引入
```java
@Configuration
//@ComponentScan("com.itheima.config")
@Import({JdbcConfig.class})
public class SpringConfig {
}
```

   - 注意: 
      - @Import参数需要的是一个数组，可以引入多个配置类。
      - @Import注解在配置类中只能写一次


### 2.3 注解开发实现为第三方bean注入资源


#### 2.3.1 简单数据类型

- 使用`@Value`注解引入值

```java
public class JdbcConfig {
    @Value("com.mysql.jdbc.Driver")
    private String driver;
    @Value("jdbc:mysql://localhost:3306/spring_db")
    private String url;
    @Value("root")
    private String userName;
    @Value("password")
    private String password;
	@Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(userName);
        ds.setPassword(password);
        return ds;
    }
}
```


##### 引入外部配置

> 1.resources目录下添加jdbc.properties
>  
> 2.配置文件中提供四个键值对分别是数据库的四要素
>  
> 3.使用@PropertySource加载jdbc.properties配置文件
>  
> 4.修改@Value注解属性的值，将其修改为`${key}`，key就是键值对中的键的值



#### 2.3.2 引用数据类型

- 只需为bean定义方法设置形参，容器会根据类型自动装配对象

```java
@Bean
public DataSource dataSource(BookDao bookDao){
    System.out.println(bookDao);
    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName(driver);
    ds.setUrl(url);
    ds.setUsername(userName);
    ds.setPassword(password);
    return ds;
}
```


# 3 注解开发总结

![](http://img.lonely.icu/lonely-md/20220814205635.png)


# 4 Spring整合


## 4.1 整合Mybatis

1. 引入依赖

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>5.2.10.RELEASE</version>
</dependency>
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.16</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.5.6</version>
</dependency>
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.47</version>
</dependency>
<dependency>
    <!--Spring操作数据库需要该jar包-->
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
<dependency>
    <!--
		Spring与Mybatis整合的jar包
		这个jar包mybatis在前面，是Mybatis提供的
	-->
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.0</version>
</dependency>
```

2. 创建Spring的主配置类

```java
//配置类注解
@Configuration
//包扫描
@ComponentScan("com.itheima")
public class SpringConfig {
}
```

3. 创建数据源的配置类

```java
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String userName;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(userName);
        ds.setPassword(password);
        return ds;
    }
}
```

4. 主配置类中读properties并引入数据源配置类

```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("classpath:jdbc.properties")
@Import(JdbcConfig.class)
public class SpringConfig {
}
```

5. 创建Mybatis配置类并配置SqlSessionFactory

```java
public class MybatisConfig {
    //定义bean，SqlSessionFactoryBean，用于产生SqlSessionFactory对象
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        //设置模型类的别名扫描
        ssfb.setTypeAliasesPackage("com.itheima.domain");
        //设置数据源
        ssfb.setDataSource(dataSource);
        return ssfb;
    }
    //定义bean，返回MapperScannerConfigurer对象
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.itheima.dao");
        return msc;
    }
}
```

![](http://img.lonely.icu/lonely-md/20220814210756.png)

![](http://img.lonely.icu/lonely-md/20220814210821.png)

6. 主配置类中引入Mybatis配置类

```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MybatisConfig.class})
public class SpringConfig {
}
```


## 4.2 整合Junit

1. 引入依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
```

2. 编写测试类

```java
//设置类运行器
@RunWith(SpringJUnit4ClassRunner.class)
//设置Spring环境对应的配置类
@ContextConfiguration(classes = {SpringConfiguration.class}) //加载配置类
//@ContextConfiguration(locations={"classpath:applicationContext.xml"})//加载配置文件
public class AccountServiceTest {
    //支持自动装配注入bean
    @Autowired
    private AccountService accountService;
    @Test
    public void testFindById(){
        System.out.println(accountService.findById(1));

    }
    @Test
    public void testFindAll(){
        System.out.println(accountService.findAll());
    }
}
```
