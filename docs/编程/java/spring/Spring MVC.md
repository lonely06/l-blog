---
title: Spring MVC
---
# 1 概述

- SpringMVC是一种基于Java实现MVC模型的轻量级Web框架
- 优点 
   - 使用简单、开发便捷(相比于Servlet)
   - 灵活性强


# 2 入门


## 2.1 步骤

1. 添加依赖
```xml
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <!--provided代表的是该包只在编译和测试的时候用，运行的时候无效直接使用tomcat中的，就避免冲突导致启动报错-->
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
  </build>
```


2. 创建配置类

```java
@Configuration
@ComponentScan("com.itheima.controller")
public class SpringMvcConfig {
}
```

3. 创建Controller类

```java
@Controller
public class UserController {
	//映射路径
    @RequestMapping("/save")
    //返回json数据
    @ResponseBody
    public String save(){
        System.out.println("user save ...");
        return "{'info':'springmvc'}";
    }
}
```

4. 使用配置类替换web.xml

```java
public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
    //加载springmvc配置类
    protected WebApplicationContext createServletApplicationContext() {
        //初始化WebApplicationContext对象
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        //加载指定配置类
        ctx.register(SpringMvcConfig.class);
        return ctx;
    }

    //设置由springmvc控制器处理的请求映射路径
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    //加载spring配置类
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
}
```


## 2.2 工作流程解析


### 2.2.1 启动服务器初始化过程

1. 服务器启动，执行ServletContainersInitConfig类，初始化web容器 
   - 功能类似于以前的web.xml
2. 执行createServletApplicationContext方法，创建了WebApplicationContext对象 
   - 该方法加载SpringMVC的配置类SpringMvcConfig来初始化SpringMVC的容器
3. 加载SpringMvcConfig配置类
4. 执行@ComponentScan加载对应的bean 
   - 扫描指定包及其子包下所有类上的注解，如Controller类上的@Controller注解
5. 加载UserController，每个@RequestMapping的名称对应一个具体的方法 
   - 此时就建立了 `/save` 和 save方法的对应关系
6. 执行getServletMappings方法，设定SpringMVC拦截请求的路径规则   ![](http://img.lonely.icu/lonely-md/20220815172610.png) 
   - `/`代表所拦截请求的路径规则，只有被拦截后才能交给SpringMVC来处理请求


### 2.2.2 单次请求过程

1. 发送请求`http://localhost/save`
2. web容器发现该请求满足SpringMVC拦截规则，将请求交给SpringMVC处理
3. 解析请求路径/save
4. 由/save匹配执行对应的方法save(） 
   - 上面的第五步已经将请求路径和方法建立了对应关系，通过/save就能找到对应的save方法
5. 执行save()
6. 检测到有@ResponseBody直接将save()方法的返回值作为响应体返回给请求方


## 2.3 bean加载控制

- SpringMVC加载其相关bean(表现层bean),也就是controller包下的类
- Spring控制的bean 
   - 业务bean(Service)
   - 功能bean(DataSource,SqlSessionFactoryBean,MapperScannerConfigurer等)


### 2.3.1 设置bean加载控制

方式一:修改Spring配置类，设定扫描范围为精准范围。

```java
@Configuration
@ComponentScan({"com.itheima.service","comitheima.dao"})
public class SpringConfig {
}
```

方式二:修改Spring配置类，设定扫描范围为com.itheima,排除掉controller包中的bean

```java
@Configuration
@ComponentScan(value="com.itheima",
    excludeFilters=@ComponentScan.Filter(
    	type = FilterType.ANNOTATION,
        classes = Controller.class
    )
)
public class SpringConfig {
}
```

- excludeFilters属性：设置扫描加载bean时，排除的过滤规则
- type属性：设置排除规则，当前使用按照bean定义时的注解类型进行排除 
   - ANNOTATION：按照注解排除
- classes属性：设置排除的具体注解类，当前设置排除@Controller定义的bean


## * 优化ServletContainersInitConfig

```java
public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {

    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```


# 3 请求与响应


## 3.1 设置请求映射路径

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/save")
    @ResponseBody
    public String save(){
        System.out.println("user save ...");
        return "{'module':'user save'}";
    }
    
    @RequestMapping("/delete")
    @ResponseBody
    public String save(){
        System.out.println("user delete ...");
        return "{'module':'user delete'}";
    }
}

@Controller
@RequestMapping("/book")
public class BookController {

    @RequestMapping("/save")
    @ResponseBody
    public String save(){
        System.out.println("book save ...");
        return "{'module':'book save'}";
    }
}
```


## 3.2 请求


### 3.2.1 参数传递


##### GET发送单个参数

- 发送请求与参数:
```
http://localhost/commonParam?name=itcast
```


- 接收参数：
```java
@Controller
public class UserController {

    @RequestMapping("/commonParam")
    @ResponseBody
    public String commonParam(String name){
        System.out.println("普通参数传递 name ==> "+name);
        return "{'module':'commonParam'}";
    }
}
```



##### GET发送多个参数

- 发送请求与参数:
```
http://localhost/commonParam?name=itcast&age=15
```


- 接收参数：
```java
@Controller
public class UserController {

    @RequestMapping("/commonParam")
    @ResponseBody
    public String commonParam(String name,int age){
        System.out.println("普通参数传递 name ==> "+name);
        System.out.println("普通参数传递 age ==> "+age);
        return "{'module':'commonParam'}";
    }
}
```



##### GET请求中文乱码

- Tomcat8.5以后的版本已经处理了中文乱码的问题，是IDEA中的Tomcat插件目前只到Tomcat7，所以需要修改pom.xml来解决GET请求中文乱码问题

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port><!--tomcat端口号-->
          <path>/</path> <!--虚拟目录-->
          <uriEncoding>UTF-8</uriEncoding><!--访问路径编解码字符集-->
        </configuration>
      </plugin>
    </plugins>
  </build>
```


##### POST发送参数

- 发送请求与参数:<br />![](http://img.lonely.icu/lonely-md/20220815175239.png)
- 接收参数：和GET一致
```java
@Controller
public class UserController {

    @RequestMapping("/commonParam")
    @ResponseBody
    public String commonParam(String name,int age){
        System.out.println("普通参数传递 name ==> "+name);
        System.out.println("普通参数传递 age ==> "+age);
        return "{'module':'commonParam'}";
    }
}
```



##### POST请求中文乱码

- 配置过滤器
```java
public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }

    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    //乱码处理，CharacterEncodingFilter是在spring-web包中
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        return new Filter[]{filter};
    }
}
```



### 3.2.2 五种类型参数传递


#### 3.2.2.1 普通参数

- 如果形参与地址参数名不一致,使用@RequestParam注解
```java
@RequestMapping("/commonParamDifferentName")
    @ResponseBody
    public String commonParamDifferentName(@RequestPaam("name") String userName , int age){
        System.out.println("普通参数传递 userName ==> "+userName);
        System.out.println("普通参数传递 age ==> "+age);
        return "{'module':'common param different name'}";
    }
```



#### 3.2.2.2 POJO数据类型

- POJO参数：请求参数名与形参对象属性名相同，定义POJO类型形参即可接收参数
```java
//请求参数key的名称要和POJO中属性的名称一致，否则无法封装
@RequestMapping("/pojoParam")
@ResponseBody
public String pojoParam(User user){
    System.out.println("pojo参数传递 user ==> "+user);
    return "{'module':'pojo param'}";
}
```



#### 3.2.2.3 嵌套POJO类型参数

- 嵌套POJO参数：请求参数名与形参对象属性名相同，按照对象层次结构关系即可接收嵌套POJO属性参数<br />![](http://img.lonely.icu/lonely-md/20220815181316.png)


#### 3.2.2.4 数组类型参数

- 数组参数：请求参数名与形参对象属性名相同且请求参数为多个，定义数组类型即可接收参数<br />![](http://img.lonely.icu/lonely-md/20220815181831.png)

- 后台接收参数:
```java
  //数组参数：同名请求参数可以直接映射到对应名称的形参数组对象中
    @RequestMapping("/arrayParam")
    @ResponseBody
    public String arrayParam(String[] likes){
        System.out.println("数组参数传递 likes ==> "+ Arrays.toString(likes));
        return "{'module':'array param'}";
    }
```



#### 3.2.2.5 集合类型参数

发送请求和参数:<br />![](http://img.lonely.icu/lonely-md/20220815181831.png)

- 后台接收参数:
```java
//集合参数：同名请求参数可以使用@RequestParam注解映射到对应名称的集合对象中作为数据
@RequestMapping("/listParam")
@ResponseBody
public String listParam(@RequestParam List<String> likes){
    System.out.println("集合参数传递 likes ==> "+ likes);
    return "{'module':'list param'}";
}
```



### 3.2.3 JSON数据传输参数


#### 3.2.3.1 JSON普通数据

1.  pom.xml添加依赖，SpringMVC默认使用的是jackson来处理json的转换 
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```


2.  开启SpringMVC注解支持 
```java
@Configuration
@ComponentScan("com.itheima.controller")
//开启json数据类型自动转换
@EnableWebMvc
public class SpringMvcConfig {
}
```


3.  参数前添加[@RequestBody ](/RequestBody )  
```java
//使用@RequestBody注解将外部传递的json数组数据映射到形参的集合对象中作为数据
@RequestMapping("/listParamForJson")
@ResponseBody
public String listParamForJson(@RequestBody List<String> likes){
    System.out.println("list common(json)参数传递 list ==> "+likes);
    return "{'module':'list common for json param'}";
}
```



#### 3.2.3.2 JSON对象数据

- 请求:
```json
{
	"name":"itcast",
	"age":15
}
```


```
- 后端接收数据：
	```java
	@RequestMapping("/pojoParamForJson")
	@ResponseBody
	public String pojoParamForJson(@RequestBody User user){
	    System.out.println("pojo(json)参数传递 user ==> "+user);
	    return "{'module':'pojo for json param'}";
	}
```
#### 3.2.3.3 JSON对象数组
- 请求和数据的发送:
	```json
	[
	    {"name":"itcast","age":15},
	    {"name":"itheima","age":12}
	]
	```
- 后端接收数据:
	```java
	@RequestMapping("/listPojoParamForJson")
	@ResponseBody
	public String listPojoParamForJson(@RequestBody List<User> list){
	    System.out.println("list pojo(json)参数传递 list ==> "+list);
	    return "{'module':'list pojo for json param'}";
	}
	```
#### @RequestBody与@RequestParam区别
  * @RequestParam用于接收url地址传参，表单传参【application/x-www-form-urlencoded】
  * @RequestBody用于接收json数据【application/json】

### 3.2.4 日期类型参数传递
- SpringMVC默认支持的字符串转日期的格式为`yyyy/MM/dd`，其他格式日期使用`@DateTimeFormat`注解
```java
@RequestMapping("/dataParam")
@ResponseBody
public String dataParam(Date date,
                        @DateTimeFormat(pattern="yyyy-MM-dd") Date date1)
    System.out.println("参数传递 date ==> "+date);
	System.out.println("参数传递 date1(yyyy-MM-dd) ==> "+date1);
    return "{'module':'data param'}";
}
```


## 3.3 响应


### 3.3.1 响应页面[了解]

```java
@Controller
public class UserController {
    
    @RequestMapping("/toJumpPage")
    //注意
    //1.此处不能添加@ResponseBody,如果加了该注入，会直接将page.jsp当字符串返回前端
    //2.方法需要返回String
    public String toJumpPage(){
        System.out.println("跳转页面");
        return "page.jsp";
    }
    
}
```


### 3.3.2 返回文本数据[了解]

```java
@Controller
public class UserController {
    
   	@RequestMapping("/toText")
	//注意此处该注解就不能省略，如果省略了,会把response text当前页面名称去查找，如果没有回报404错误
    @ResponseBody
    public String toText(){
        System.out.println("返回纯文本数据");
        return "response text";
    }
    
}
```


### 3.2.3 响应JSON数据

1. 响应POJO对象
```java
@Controller
public class UserController {
    @RequestMapping("/toJsonPOJO")
    @ResponseBody
    public User toJsonPOJO(){
        System.out.println("返回json对象数据");
        User user = new User();
        user.setName("itcast");
        user.setAge(15);
        return user;
    }
    
}
```


2. 响应POJO集合对象
```java
@Controller
public class UserController {
    
    @RequestMapping("/toJsonList")
    @ResponseBody
    public List<User> toJsonList(){
        System.out.println("返回json集合数据");
        User user1 = new User();
        user1.setName("传智播客");
        user1.setAge(15);

        User user2 = new User();
        user2.setName("黑马程序员");
        user2.setAge(12);

        List<User> userList = new ArrayList<User>();
        userList.add(user1);
        userList.add(user2);

        return userList;
    }
    
}
```



### [@ReponseBody ](/ReponseBody ) 

- 可以写在类上或者方法上
- 写在类上就是该类下的所有方法都有@ReponseBody功能
- 当方法上有@ReponseBody注解后 
   - 方法的返回值为字符串，会将其作为文本内容直接响应给前端
   - 方法的返回值为对象，会将对象转换成JSON响应给前端


# 4 Rest风格


## 4.1 简介

- REST（Representational State Transfer），表现形式状态转换,它是一种软件架构风格

- 优点 
   - 隐藏资源的访问行为，无法通过地址得知对资源是何种操作
   - 书写简化

- 根据REST风格对资源进行访问称为RESTful。


## 4.2 语法

- 使用`@RestController`注解替换@Controller与@ResponseBody注解
- 查询：`@GetMapping`
- 新增：`@PostMapping`
- 修改：`@PutMapping`
- 删除：`@DeleteMapping`


## 4.3 传递路径参数

- 请求`http://localhost/users/1`,路径中的`1`为参数。 
   - 修改@RequestMapping的value属性，将其中修改为`/users/{id}`
   - 在方法的形参前添加@PathVariable注解
```java
@Controller
public class UserController {
    //设置当前请求方法为DELETE，表示REST风格中的删除操作
	@RequestMapping(value = "/users/{id}",method = RequestMethod.DELETE)
    @ResponseBody
    public String delete(@PathVariable Integer id) {
        System.out.println("user delete..." + id);
        return "{'module':'user delete'}";
    }
}
```

- 请求`http://localhost/users/1/tom`,路径中的`1`和`tom`均为参数。

```java
	@Controller
	public class UserController {
	    //设置当前请求方法为DELETE，表示REST风格中的删除操作
		@RequestMapping(value = "/users/{id}/{name}",method = RequestMethod.DELETE)
	    @ResponseBody
	    public String delete(@PathVariable Integer id,@PathVariable String name) {
	        System.out.println("user delete..." + id+","+name);
	        return "{'module':'user delete'}";
	    }
	}
```


## @RequestBody、@RequestParam、@PathVariable区别

- @RequestParam用于接收url地址传参或表单传参
- @RequestBody用于接收json数据
- @PathVariable用于接收路径参数，使用{参数名称}描述路径参数


## SpringMVC需要将静态资源进行放行

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    //设置静态资源访问过滤，当前类需要设置为配置类，并被扫描加载
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        //当访问/pages/????时候，从/pages目录下查找内容
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```


# 5 SSM整合

1. 添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.itheima</groupId>
  <artifactId>springmvc_08_ssm</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

2. 创建项目包结构
3. 创建SpringConfig配置类

```java
@Configuration
@ComponentScan({"com.itheima.service"})
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MyBatisConfig.class})
@EnableTransactionManagement
public class SpringConfig {
}
```

4. 创建JdbcConfig配置类

```java
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager ds = new DataSourceTransactionManager();
        ds.setDataSource(dataSource);
        return ds;
    }
}
```

5. 创建MybatisConfig配置类

```java
public class MyBatisConfig {
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setTypeAliasesPackage("com.itheima.domain");
        return factoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.itheima.dao");
        return msc;
    }
}
```

6. 创建jdbc.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_db
jdbc.username=root
jdbc.password=root
```

7. 创建SpringMVC配置类

```java
@Configuration
@ComponentScan("com.itheima.controller")
@EnableWebMvc
public class SpringMvcConfig {
}
```

8. 创建Web项目入口配置类

```java
public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    //加载Spring配置类
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }
    //加载SpringMVC配置类
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }
    //设置SpringMVC请求地址拦截规则
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
    //设置post请求中文乱码过滤器
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("utf-8");
        return new Filter[]{filter};
    }
}
```


# 6 统一结果封装

1. 创建Result类

```java
public class Result {
    //描述统一格式中的数据
    private Object data;
    //描述统一格式中的编码，用于区分操作，可以简化配置0或1表示成功失败
    private Integer code;
    //描述统一格式中的消息，可选属性
    private String msg;

    public Result() {
    }
	//构造方法是方便对象的创建
    public Result(Integer code,Object data) {
        this.data = data;
        this.code = code;
    }
	//构造方法是方便对象的创建
    public Result(Integer code, Object data, String msg) {
        this.data = data;
        this.code = code;
        this.msg = msg;
    }
	//setter...getter...省略
}
```

2. 定义返回码Code类

```java
//状态码
public class Code {
    public static final Integer SAVE_OK = 20011;
    public static final Integer DELETE_OK = 20021;
    public static final Integer UPDATE_OK = 20031;
    public static final Integer GET_OK = 20041;

    public static final Integer SAVE_ERR = 20010;
    public static final Integer DELETE_ERR = 20020;
    public static final Integer UPDATE_ERR = 20030;
    public static final Integer GET_ERR = 20040;
}
```


# 7 统一异常处理

- [@RestControllerAdvice ](/RestControllerAdvice )  
   - 此注解自带@ResponseBody注解与@Component注解，具备对应的功能
- [@ExceptionHandler ](/ExceptionHandler ) <br />此类方法可以根据处理的异常不同，制作多个方法分别处理对应的异常


## 7.1 异常解决方案

- 业务异常（BusinessException） 
   - 发送对应消息传递给用户，提醒规范操作 
      - 大家常见的就是提示用户名已存在或密码格式不正确等
- 系统异常（SystemException） 
   - 发送固定消息传递给用户，安抚用户 
      - 系统繁忙，请稍后再试
      - 系统正在维护升级，请稍后再试
      - 系统出问题，请联系系统管理员等
   - 发送特定消息给运维人员，提醒维护 
      - 可以发送短信、邮箱或者是公司内部通信软件
   - 记录日志 
      - 发消息和记录日志对用户来说是不可见的，属于后台程序
- 其他异常（Exception） 
   - 发送固定消息传递给用户，安抚用户
   - 发送特定消息给编程人员，提醒维护（纳入预期范围内） 
      - 一般是程序没有考虑全，比如未做非空校验等
   - 记录日志


## 7.2 异常解决步骤

1. 自定义异常类

```java
//自定义异常处理器，用于封装异常信息，对异常进行分类
public class SystemException extends RuntimeException{
    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public SystemException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }

}

//自定义异常处理器，用于封装异常信息，对异常进行分类
public class BusinessException extends RuntimeException{
    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public BusinessException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public BusinessException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }

}
```

2. 将其他异常包成自定义异常 
   - 方式一:`try{}catch(){}`在catch中重新throw我们自定义异常即可。
   - 方式二:直接throw自定义异常即可

```java
public Book getById(Integer id) {
    //模拟业务异常，包装成自定义异常
    if(id == 1){
        throw new BusinessException(Code.BUSINESS_ERR,"请不要使用你的技术挑战我的耐性!");
    }
    //模拟系统异常，将可能出现的异常进行包装，转换成自定义异常
    try{
        int i = 1/0;
    }catch (Exception e){
        throw new SystemException(Code.SYSTEM_TIMEOUT_ERR,"服务器访问超时，请重试!",e);
    }
    return bookDao.getById(id);
}
```

3. 处理器类中处理自定义异常

```java
//@RestControllerAdvice用于标识当前类为REST风格对应的异常处理器
@RestControllerAdvice
public class ProjectExceptionAdvice {
    //@ExceptionHandler用于设置当前处理器类对应的异常类型
    @ExceptionHandler(SystemException.class)
    public Result doSystemException(SystemException ex){
        //记录日志
        //发送消息给运维
        //发送邮件给开发人员,ex对象发送给开发人员
        return new Result(ex.getCode(),null,ex.getMessage());
    }

    @ExceptionHandler(BusinessException.class)
    public Result doBusinessException(BusinessException ex){
        return new Result(ex.getCode(),null,ex.getMessage());
    }

    //除了自定义的异常处理器，保留对Exception类型的异常处理，用于处理非预期的异常
    @ExceptionHandler(Exception.class)
    public Result doOtherException(Exception ex){
        //记录日志
        //发送消息给运维
        //发送邮件给开发人员,ex对象发送给开发人员
        return new Result(Code.SYSTEM_UNKNOW_ERR,null,"系统繁忙，请稍后再试！");
    }
}
```

![](http://img.lonely.icu/lonely-md/20220816150306.png)


# 8 拦截器


## 8.1 拦截器概念

![](http://img.lonely.icu/lonely-md/20220816150657.png)

- 拦截器（Interceptor）是一种动态拦截方法调用的机制，在SpringMVC中动态拦截控制器方法的执行
- 作用：对原始方法进行增强

- 拦截器和过滤器的区别 
   - 归属不同：Filter属于Servlet技术，Interceptor属于SpringMVC技术
   - 拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强<br />![](http://img.lonely.icu/lonely-md/20220816150711.png)


## 8.2 拦截器入门

1. 创建拦截器类

```java
@Component
//定义拦截器类，实现HandlerInterceptor接口
//注意当前类必须受Spring容器控制
public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    //原始方法调用前执行的内容
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        return true;
    }

    @Override
    //原始方法调用后执行的内容
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }

    @Override
    //原始方法调用完成后执行的内容
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```

2. 配置拦截器类

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        //配置拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books" );
    }
}
```

3. SpringMVC添加SpringMvcSupport包扫描

```java
@Configuration
@ComponentScan({"com.itheima.controller","com.itheima.config"})
@EnableWebMvc
public class SpringMvcConfig{
   
}
```

拦截器的执行流程:<br />![](http://img.lonely.icu/lonely-md/20220816151124.png)


## 8.3 拦截器参数


### 8.3.1 前置处理方法

- request:请求对象
- response:响应对象
- handler:被调用的处理器对象，本质上是一个方法对象，对反射中的Method对象进行了再包装 
   - 使用handler参数，可以获取方法的相关信息
```java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    HandlerMethod hm = (HandlerMethod)handler;
    String methodName = hm.getMethod().getName();//可以获取方法的名称
    System.out.println("preHandle..."+methodName);
    return true;
}
```


### 8.3.2 后置处理方法

前三个参数和上面的是一致的。<br />modelAndView:如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整


### 8.3.3 完成处理方法

前三个参数和上面的是一致的。<br />Exception:如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理


## 8.4 多拦截器配置

- 配置多拦截器

```java
@Configuration
@ComponentScan({"com.itheima.controller"})
@EnableWebMvc
//实现WebMvcConfigurer接口可以简化开发，但具有一定的侵入性
public class SpringMvcConfig implements WebMvcConfigurer {
    @Autowired
    private ProjectInterceptor projectInterceptor;
    @Autowired
    private ProjectInterceptor2 projectInterceptor2;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //配置多拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books","/books/*");
        registry.addInterceptor(projectInterceptor2).addPathPatterns("/books","/books/*");
    }
}
```

![](http://img.lonely.icu/lonely-md/20220816151741.png)
