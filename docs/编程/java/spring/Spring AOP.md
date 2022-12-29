---
title: Spring AOP
---

## 1 AOP简介

- 概念： 
   - AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，指导开发者如何组织程序结构。 
      - OOP(Object Oriented Programming)面向对象编程
- 作用:在不惊动原始设计的基础上为方法进行功能增强
- 核心概念 
   - 代理（Proxy）：SpringAOP的核心本质是采用代理模式实现的
   - 连接点（JoinPoint）：在SpringAOP中，理解为任意方法的执行
   - 切入点（Pointcut）：匹配连接点的式子，也是具有共性功能的方法描述
   - 通知（Advice）：若干个方法的共性功能，在切入点处执行，最终体现为一个方法
   - 切面（Aspect）：描述通知与切入点的对应关系
   - 目标对象（Target）：被代理的原始对象成为目标对象<br />![](http://img.lonely.icu/lonely-md/20220815154430.png)


## 2 AOP快速入门

1. 添加依赖

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

2. 定义通知类，通知和切入点

```java
//通知类，类名和方法名没有要求，可以任意
public class MyAdvice {
	//有两个方法，分别是save和update，增强update方法
	//切入点
    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){}
    //通知
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

3. 绑定切入点与通知关系，将通知类配给容器并标识其为切面类

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){}
    
    @Around("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

4. 开启注解格式AOP功能

```java
@Configuration
@ComponentScan("com.itheima")
@EnableAspectJAutoProxy
public class SpringConfig {
}
```


## 3 AOP工作流程

1.  Spring容器启动 
   - 容器启动就需要去加载bean，即需要被增强的类，通知类。此时bean对象还没有创建成功
2.  读取所有切面配置中的切入点 
   - 只读取被使用的切入点
3.  初始化bean 
   - 要被实例化bean对象的类中的方法和切入点进行匹配 
      - 匹配失败，创建原始对象，直接调用原始对象的方法
      - 匹配成功，创建原始对象（目标对象）的代理对象 
         - 最终运行的是代理对象的方法，在该方法中会对原始方法进行功能增强<br />![](http://img.lonely.icu/lonely-md/20220815155813.png)
4.  获取bean执行方法 
   - 获取的bean是原始对象时，调用方法并执行，完成操作
   - 获取的bean是代理对象时，根据代理对象的运行模式运行原始方法与增强的内容，完成操作

- 目标对象就是要增强的类对应的对象，也叫原始对象，在运行的过程中对于要增强的内容是缺失的。
- SpringAOP是在不改变原有设计(代码)的前提下对其进行增强的，它的底层采用的是代理模式实现的，所以要对原始对象进行增强，就需要对原始对象创建代理对象，在代理对象中的方法把通知[如:MyAdvice中的method方法]内容加进去，就实现了增强,这就是我们所说的代理(Proxy)。


## 4. AOP配置管理


### 4.1 AOP切入点表达式

- 切入点表达式标准格式：动作关键字(访问修饰符  返回值  包名.类/接口名.方法名（参数）异常名)
```
execution(* com.itheima.service.*Service.*(..))
```


- 切入点表达式描述通配符： 
   - `*`：匹配任意符号（常用）
   - `..` ：匹配多个连续的任意符号（常用）
   - `+`：匹配子类类型
```java
  execution（public * com.itheima.*.UserService.find*(*))
  execution（public User com..UserService.findById(..))
  execution(* *..*Service+.*(..))
```

- 切入点表达式书写技巧 
   1. 查询操作的返回值建议使用`*`匹配，增删改类使用精准类型
   2. 减少使用..的形式描述包
   3. 描述切入点通常描述接口，而不描述实现类

- 常用切入点表达式的编写规则：
```java
execution(* com.itheima.*.*Service.find*(..))
将项目中所有业务层方法的以find开头的方法匹配
execution(* com.itheima.*.*Service.save*(..))
将项目中所有业务层方法的以save开头的方法匹配
```



### 4.2 AOP通知类型

- 共5种通知类型: 
   1. 前置通知`@Before`
   2. 后置通知`@After`
   3. **环绕通知(重点)**`@Around`
   4. 返回后通知(了解)`@AfterReturning`
   5. 抛出异常后通知(了解)`@AfterThrowing`<br />![](http://img.lonely.icu/lonely-md/20220815160744.png)
- 注意： 
   1. 环绕通知在原始方法的前后进行增强，须对原始操作进行调用
```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){}
    
    @Around("pt()")
    public void around(ProceedingJoinPoint pjp) throws Throwable{
        System.out.println("around before advice ...");
        //表示对原始操作的调用
        pjp.proceed();
        System.out.println("around after advice ...");
    }
}
```

   2. 环绕通知必须依赖形参ProceedingJoinPoint才能实现对原始方法的调用，进而实现原始方法调用前后同时添加通知
   3. 对原始方法的调用可以不接收返回值，通知方法设置成void即可，如果接收返回值，最好设定为Object类型
   4. 环绕通知方法必须处理Throwable异常
   5. 返回后通知需原始方法正常执行后才会被执行，如果方法执行的过程中出现了异常，那么返回后通知是不会被执行。后置通知是不管原始方法有没有抛出异常都会被执行。


### 4.3 AOP通知获取数据


#### 4.3.1 获取参数


##### 非环绕通知获取方式

- 在方法上添加JoinPoint,通过JoinPoint来获取参数，适用于`前置`、`后置`、`返回后`、`抛出异常后`通知

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @Before("pt()")
    public void before(JoinPoint jp) 
        Object[] args = jp.getArgs();
        System.out.println(Arrays.toString(args));
        System.out.println("before advice ..." );
    }
	//...其他的略
}
```


##### 环绕通知获取方式

- 使用ProceedingJoinPoint中的`getArgs()`方法

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @Around("pt()")
    public Object around(ProceedingJoinPoint pjp)throws Throwable {
        Object[] args = pjp.getArgs();
        System.out.println(Arrays.toString(args));
        Object ret = pjp.proceed();
        return ret;
    }
}
```

- 注意: 
   - proceed()方法有两个构造方法  ![](http://img.lonely.icu/lonely-md/20220815160946.png)
   - 无参数的proceed，当原始方法有参数，会在调用的过程中自动传入参数
   - 当需要修改原始方法的参数时，采用带有参数的方法


#### 4.3.2 获取返回值


##### 环绕通知获取返回值

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @Around("pt()")
    public Object around(ProceedingJoinPoint pjp) throws Throwable{
        Object[] args = pjp.getArgs();
        args[0] = 666;
        //获取返回值
        Object ret = pjp.proceed(args);
        return ret;
    }
}
```


##### 返回后通知获取返回值

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @AfterReturning(value = "pt()",returning = "ret")
    public void afterReturning(Object ret) {
        System.out.println("afterReturning advice ..."+ret);
    }
}
```

- 注意: 
   1. 参数名要一致![](http://img.lonely.icu/lonely-md/20220815161054.png)
   2. afterReturning方法参数的顺序![](http://img.lonely.icu/lonely-md/20220815161101.png)


#### 4.3.3 获取异常(了解)


##### 环绕通知获取异常

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @Around("pt()")
    public Object around(ProceedingJoinPoint pjp){
        Object[] args = pjp.getArgs();
        args[0] = 666;
        Object ret = null;
        try{
            ret = pjp.proceed(args);
        }catch(Throwable throwable){
            t.printStackTrace();
        }
        return ret;
    }
}
```


##### 抛出异常后通知获取异常

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(* com.itheima.dao.BookDao.findName(..))")
    private void pt(){}

    @AfterThrowing(value = "pt()",throwing = "t")
    public void afterThrowing(Throwable t) {
        System.out.println("afterThrowing advice ..."+t);
    }
}
```


## 5 AOP事务管理


### 5.1 Spring事务简介


#### 5.1.1 相关概念

- 事务作用：在数据层保障一系列的数据库操作同成功同失败
- Spring事务作用：在数据层或业务层保障一系列的数据库操作同成功同失败


#### 5.1.2 事务管理步骤

1. 在需要被事务管理的方法上添加注解`@Transactional`
```java
public interface AccountService {
    /**
     * 转账操作
     * @param out 传出方
     * @param in 转入方
     * @param money 金额
     */
    //配置当前接口方法具有事务
    public void transfer(String out,String in ,Double money) ;
}

@Service
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;
	@Transactional
    public void transfer(String out,String in ,Double money) {
        accountDao.outMoney(out,money);
        int i = 1/0;
        accountDao.inMoney(in,money);
    }

}
```


   - 注意: 
      - @Transactional可以写在接口类上、接口方法上、实现类上和实现类方法上 
         - 写在接口类上，该接口的所有实现类的所有方法都会有事务
         - 写在接口方法上，该接口的所有实现类的该方法都会有事务
         - 写在实现类上，该类中的所有方法都会有事务
         - 写在实现类方法上，该方法上有事务
         - 建议写在实现类或实现类的方法上
2. 在JdbcConfig类中配置事务管理器
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

    //配置事务管理器，mybatis使用的是jdbc事务
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```


   - 注意：事务管理器要根据使用技术进行选择，Mybatis框架使用的是JDBC事务，可以直接使用`DataSourceTransactionManager`
3. 开启事务注解
```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MybatisConfig.class
//开启注解式事务驱动
@EnableTransactionManagement
public class SpringConfig {
}
```



### 5.2 Spring事务角色

- 事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法
- 事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法


### 5.3 Spring事务属性


#### 5.3.1 事务配置

- 在`@Transactional`注解的参数上进行设置![](http://img.lonely.icu/lonely-md/20220815164440.png)

- readOnly：true只读事务，false读写事务，增删改要设为false,查询设为true。
- timeout:设置超时时间单位秒，在多长时间之内事务没有提交成功就自动回滚，-1表示不设置超时时间。
- rollbackFor:当出现指定异常进行事务回滚
- noRollbackFor:当出现指定异常不进行事务回滚 
   - Spring的事务只会对`Error异常`和`RuntimeException异常`及其子类进行事务回顾，其他的异常类型是不会回滚的
- rollbackForClassName等同于rollbackFor,只不过属性为异常的类全名字符串
- noRollbackForClassName等同于noRollbackFor，只不过属性为异常的类全名字符串
- isolation设置事务的隔离级别 
   - DEFAULT : 默认隔离级别, 会采用数据库的隔离级别
   - READ_UNCOMMITTED : 读未提交
   - READ_COMMITTED : 读已提交
   - REPEATABLE_READ : 重复读取
   - SERIALIZABLE : 串行化


#### 5.3.2 事务传播行为

- 事务传播行为：事务协调员对事务管理员所携带事务的处理态度。
- 修改logService改变事务的传播行为
```java
@Service
public class LogServiceImpl implements LogService {

    @Autowired
    private LogDao logDao;
	//propagation设置事务属性：传播行为设置为当前操作需要新事务
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void log(String out,String in,Double money ) {
        logDao.log("转账操作由"+out+"到"+in+",金额："+money);
    }
}
```


- 事务传播行为的可选值![](http://img.lonely.icu/lonely-md/20220815163724.png)
