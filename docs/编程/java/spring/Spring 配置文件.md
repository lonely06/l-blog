---
title: Spring 配置文件
---
# 1 Spring


## 1.1 官网

[官网](https://spring.io/)


## 1.2 系统架构图

- Spring Framework的5版本目前没有最新的架构图，而最新的是4版本，所以接下来主要研究的是4的架构图

![](http://img.lonely.icu/lonely-md/20220813161550.png)

(1)核心层

- Core Container:核心容器，这个模块是Spring最核心的模块，其他的都需要依赖该模块

(2)AOP层

- AOP:面向切面编程，它依赖核心层容器，目的是在不改变原有代码的前提下对其进行功能增强
- Aspects:AOP是思想,Aspects是对AOP思想的具体实现

(3)数据层

- Data Access:数据访问，Spring全家桶中有对数据访问的具体实现技术
- Data Integration:数据集成，Spring支持整合其他的数据层解决方案，比如Mybatis
- Transactions:事务，Spring中事务管理是Spring AOP的一个具体实现，也是后期学习的重点内容

(4)Web层

- 这一层的内容将在SpringMVC框架具体学习

(5)Test层

- Spring主要整合了Junit来完成单元测试和集成测试


# 2 Spring核心概念


## IOC、IOC容器、Bean、DI

1.  IOC（Inversion of Control）控制反转 
   1.  什么是控制反转？ 
      - 使用对象时，由主动new产生对象转换为由外部提供对象，此过程中对象创建控制权由程序转移到外部，此思想称为控制反转。
   2.  Spring和IOC之间的关系是什么呢? 
      - Spring技术对IOC思想进行了实现
      - Spring提供了一个容器，称为IOC容器，用来充当IOC思想中的"外部"
      - IOC思想中的`别人[外部]`指的就是Spring的IOC容器
   3.  IOC容器的作用以及内部存放的是什么? 
      - IOC容器负责对象的创建、初始化等一系列工作，其中包含了数据层和业务层的类对象
      - 被创建或被管理的对象在IOC容器中统称为Bean
      - IOC容器中放的就是一个个的Bean对象
   4.  当IOC容器中创建好service和dao对象后，程序能正确执行么? 
      - 不行，因为service运行需要依赖dao对象
      - IOC容器中虽然有service和dao对象
      - 但是service对象和dao对象没有任何关系
      - 需要把dao对象交给service,也就是说要绑定service和dao对象之间的关系
2.  DI（Dependency Injection）依赖注入 
   -  什么是依赖注入? 
      - 在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入
   -  IOC和DI最终目标就是:充分解耦，具体实现靠: 
      - 使用IOC容器管理bean（IOC)
      - 在IOC容器内将有依赖关系的bean进行关系绑定（DI）
   -  结果:使用对象时不仅可以直接从IOC容器中获取，并且获取到的bean已经绑定了所有的依赖关系. 

(1)什么IOC/DI思想?

- IOC:控制反转，控制反转的是对象的创建权
- DI:依赖注入，绑定对象与对象之间的依赖关系

(2)什么是IOC容器?

Spring创建了一个容器用来存放所创建的对象，这个容器就叫IOC容器

(3)什么是Bean?

容器中所存放的一个个对象就叫Bean或Bean对象


# 3 快速入门


## 3.1 IOC入门案例

1. 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

2. 添加spring配置文件<br />resources下添加spring配置文件applicationContext.xml

![](http://img.lonely.icu/lonely-md/20220813163632.png)

3. 在配置文件中完成bean的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
					       http://www.springframework.org/schema/beans/spring-beans.xsd">
 
    <!--bean标签标示配置bean
    	id属性标示给bean起名字
    	class属性表示给bean定义类型
    	
    	注意：bean定义时id属性在同一个上下文中(配置文件)不能重复
	-->
	<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"/>

</beans>
```

6. 获取IOC容器，并从容器中获取对象进行方法调用

```java
public class App {
    public static void main(String[] args) {
        //获取IOC容器
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml"); 
//        BookDao bookDao = (BookDao) ctx.getBean("bookDao");
//        bookDao.save();
        BookService bookService = (BookService) ctx.getBean("bookService");
        bookService.save();
    }
}
```


## 3.2 DI入门案例

1. 删除业务层中使用new的方式创建的dao对象

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

2. 为属性提供setter方法

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
    //提供对应的set方法
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}
```

3. 修改配置完成注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--bean标签标示配置bean
    	id属性标示给bean起名字
    	class属性表示给bean定义类型
	-->
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <!--配置server与dao的关系-->
        <!--property标签表示配置当前bean的属性
        		name属性表示配置哪一个具体的属性
        		ref属性表示参照哪一个bean
		-->
        <property name="bookDao" ref="bookDao"/>
    </bean>

</beans>
```

- name="bookDao"中`bookDao`的作用是让Spring的IOC容器在获取到名称后，将首字母大写，前面加set找对应的`setBookDao()`方法进行对象注入
- ref="bookDao"中`bookDao`的作用是让Spring能在IOC容器中找到id为`bookDao`的Bean对象给`bookService`进行注入


# 4 IOC


### 4.1 bean基础配置

![](http://img.lonely.icu/lonely-md/20220813170359.png)


#### 4.1.1 bean基础配置(id与class)

![](http://img.lonely.icu/lonely-md/20220813165530.png)

- 注意 
   - 不能使用接口来作为class属性（接口不能创建对象）


#### 4.1.2 bean的name属性

![](http://img.lonely.icu/lonely-md/20220813165852.png)

- 注意 
   - bean依赖注入的ref属性指定bean，必须在容器中存在


#### 4.1.3 bean作用范围scope配置

![](http://img.lonely.icu/lonely-md/202211251613516.png)

- 为什么bean默认为单例? 
   - bean对象只有一个就避免了对象的频繁创建与销毁，达到了bean对象的复用，性能高
- bean在容器中是单例的，会不会产生线程安全问题? 
   - 如果对象是有状态对象，即该对象有成员变量可以用来存储数据的，因为所有请求线程共用一个bean对象，所以会存在线程安全问题。
   - 如果对象是无状态对象，即该对象没有成员变量没有进行数据存储的，因方法中的局部变量在方法调用完成后会被销毁，所以不会存在线程安全问题。
- 哪些bean对象适合交给容器进行管理? 
   - 表现层对象
   - 业务层对象
   - 数据层对象
   - 工具对象
- 哪些bean对象不适合交给容器进行管理? 
   - 封装实例的域对象，因为会引发线程安全问题，所以不适合。


### 4.2 bean实例化


#### 4.2.1 构造方法实例化

- Spring底层使用： 
   1. 反射，可以访问类中的私有构造方法
   2. 类的无参构造方法


#### 4.2.2 静态工厂实例化(了解)

1. 创建工厂类

```java
//静态工厂创建对象
public class OrderDaoFactory {
    public static OrderDao getOrderDao(){
        return new OrderDaoImpl();
    }
}
```

2. 在spring的配置文件application.properties中添加以下内容:

```xml
<!--
	class:工厂类的类全名
	factory-mehod:具体工厂类中创建对象的方法名
-->
<bean id="orderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"/>
```

- 使用静态工厂实列化原因: 
   - 在工厂的静态方法中，除了new对象还可以做其他的一些业务操作


#### 4.2.3 实例工厂与FactoryBean(了解)

1. 创建工厂类并提供一个普通方法，注意此处和静态工厂的工厂类不一样的地方是方法不是静态方法

```java
public class UserDaoFactory {
    public UserDao getUserDao(){
        return new UserDaoImpl();
    }
}
```

2. 在spring的配置文件中添加以下内容:

```xml
<!--
	factory-bean:工厂的实例对象
	factory-method:工厂对象中的具体创建对象的方法名
-->
<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>
<bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
```

- 实例化工厂运行的顺序: 
   - 创建实例化工厂对象,对应的是第一行配置
   - 调用对象中的方法来创建bean，对应的是第二行配置
- Spring为了简化这种配置方式就提供了一种叫`FactoryBean`的方式来简化开发。


##### 4.2.3.1 FactoryBean的使用(实用)

1. 创建一个类，实现FactoryBean接口，重写接口的方法

```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    //代替原始实例工厂中创建对象的方法
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }
    //返回所创建类的Class对象
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
//若需修改bean的作用范围，重写isSingleton()方法
```

2. 在Spring的配置文件中进行配置

```xml
<bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```


### 4.3 bean的生命周期

1.  Spring中对bean生命周期控制提供了两种方式: 
   - 在配置文件中的bean标签中添加`init-method`和`destroy-method`属性
   - 类实现`InitializingBean`与`DisposableBean`接口，了解下即可。
2.  对于bean的生命周期控制在bean的整个生命周期中所处的位置如下: 
   - 初始化容器 
      - 1.创建对象(内存分配)
      - 2.执行构造方法
      - 3.执行属性注入(set操作)
      - 4.执行bean初始化方法
   - 使用bean 
      - 1.执行业务操作
   - 关闭/销毁容器 
      - 1.执行bean销毁方法
3.  关闭容器的两种方式: 
   - ConfigurableApplicationContext是ApplicationContext的子类 
      - close()方法
      - registerShutdownHook()方法
      - 两种方法的异同 
         - 相同点:这两种都能用来关闭容器
         - 不同点:close()是在调用的时候关闭，registerShutdownHook()是在JVM退出前调用关闭。


# 5 DI相关内容

-  setter注入 
   - 简单数据类型
```xml
<bean ...>
	<property name="" value=""/>
</bean>
```


   - 引用数据类型
```xml
<bean ...>
	<property name="" ref=""/>
</bean>
```


-  构造器注入 
   - 简单数据类型
```xml
<bean ...>
	<constructor-arg name="" value=""/>
</bean>
```


   - 引用数据类型
```xml
<bean ...>
	<constructor-arg name="" ref=""/>
</bean>
```


-  依赖注入的方式选择上 
   - 建议使用setter注入
   - 第三方技术根据情况选择


### 5.1 setter注入

- 对于引用数据类型使用的是`<property name="" ref=""/>`
- 对于简单数据类型使用的是`<property name="" value=""/>`


#### 5.1.1 注入引用数据类型

1. 声明属性并提供setter方法
2. 配置文件中进行注入配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <property name="bookDao" ref="bookDao"/>
        <property name="userDao" ref="userDao"/>
    </bean>
</beans>
```


#### 5.1.2 注入简单数据类型

1. 声明属性并提供setter方法
2. 配置文件中进行注入配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
        <property name="databaseName" value="mysql"/>
     	<property name="connectionNum" value="10"/>
    </bean>
</beans>
```


### 5.2 构造器注入


#### 5.2.1 构造器注入引用数据类型

1. 删除setter方法并提供构造方法
2. 配置文件中进行配置构造方式注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
		<!--
			name属性对应的值为构造函数中方法形参的参数名，必须要保持一致。
			ref属性指向的是spring的IOC容器中其他bean对象。
		-->
        <constructor-arg name="bookDao" ref="bookDao"/>
    </bean>
</beans>
```


#### 5.2.2 构造器注入多个引用数据类型

1. 提供多个属性的构造函数
2. 配置文件中配置多参数注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
		<!--这两个<contructor-arg>的配置顺序可以任意-->
        <constructor-arg name="bookDao" ref="bookDao"/>
        <constructor-arg name="userDao" ref="userDao"/>
    </bean>
</beans>
```


#### 5.2.3 构造器注入多个简单数据类型

1.  添加多个简单属性并提供构造方法 
2.  配置完成多个属性构造器注入 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
	    <!--这两个<contructor-arg>的配置顺序可以任意-->
        <constructor-arg name="databaseName" value="mysql"/>
        <constructor-arg name="connectionNum" value="666"/>
    </bean>
</beans>
```

- 当构造函数中方法的参数名发生变化后，配置文件中的name属性也需要跟着变，如何解决？ 
   - 方式一:删除name属性，添加type属性，按照类型注入

### 5.3 自动配置

- 自动装配： IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中的过程称为自动装配 
   1. 自动装配用于**引用类型依赖注入**，不能对简单类型进行操作
   2. 使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用 
      1. 需要注入属性的类中对应属性的setter方法不能省略
      2. 被注入的对象必须要被Spring的IOC容器管理
   3. 使用按名称装配时（byName）必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用
   4. 自动装配优先级低于setter注入与构造器注入，同时出现时自动装配配置失效

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="com.itheima.dao.impl.BookDaoImpl"/>
    <!--autowire属性：开启自动装配，通常使用按类型装配-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byType"/>

	<!--autowire属性：开启自动装配，按名称-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byName"/>

</beans>
```


### 5.4 集合注入


#### 5.4.2 注入数组类型数据

```xml
<property name="array">
    <array>
        <value>100</value>
        <value>200</value>
        <value>300</value>
    </array>
</property>
```


#### 5.4.3 注入List类型数据

```xml
<property name="list">
    <list>
        <value>itcast</value>
        <value>itheima</value>
        <value>boxuegu</value>
        <value>chuanzhihui</value>
    </list>
</property>
```


#### 5.4.4 注入Set类型数据

```xml
<property name="set">
    <set>
        <value>itcast</value>
        <value>itheima</value>
        <value>boxuegu</value>
        <value>boxuegu</value>
    </set>
</property>
```


#### 5.4.5 注入Map类型数据

```xml
<property name="map">
    <map>
        <entry key="country" value="china"/>
        <entry key="province" value="henan"/>
        <entry key="city" value="kaifeng"/>
    </map>
</property>
```


#### 5.4.6 注入Properties类型数据

```xml
<property name="properties">
    <props>
        <prop key="country">china</prop>
        <prop key="province">henan</prop>
        <prop key="city">kaifeng</prop>
    </props>
</property>
```

- 注意 
   - property标签表示setter方式注入，构造方式注入constructor-arg标签内部也可以写`<array>`、`<list>`、`<set>`、`<map>`、`<props>`标签
   - List的底层也是通过数组实现的，所以`<list>`和`<array>`标签是可以混用
   - 集合中要添加引用类型，只需要把`<value>`标签改成`<ref>`标签，这种方式用的比较少


# 6 IOC/DI配置管理第三方bean


## 6.1 数据源对象管理


### 6.1.1 Druid管理

1. pom.xml添加依赖

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
```

2. 在applicationContext.xml配置文件中添加`DruidDataSource`的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--管理DruidDataSource对象-->
    <bean class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/spring_db"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>
</beans>
```

3. 从IOC容器中获取对应的bean对象

```java
public class App {
    public static void main(String[] args) {
       ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
       DataSource dataSource = (DataSource) ctx.getBean("dataSource");
       System.out.println(dataSource);
    }
}
```


### 6.1.2 C3P0管理

1. pom.xml中添加依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
```

2. 在applicationContext.xml配置文件中添加配置

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_db"/>
    <property name="user" value="root"/>
    <property name="password" value="root"/>
    <property name="maxPoolSize" value="1000"/>
</bean>
```


## 6.2 加载properties文件

1. 准备properties配置文件

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/spring_db
jdbc.username=root
jdbc.password=root
```

2. 开启`context`命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

3. 加载properties配置文件

```xml
<context:property-placeholder location="jdbc.properties"/>
```

4. 使用`${key}`来读取properties配置文件中的内容并完成属性注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
    
    <context:property-placeholder location="jdbc.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```


### 6.2.1 注意事项

1. 键值对的key为`username`引发的问题 
   - 在properties中配置键值对的时候，如果key设置为`username`，得到的是电脑的用户名
   - 原因:`<context:property-placeholder/>`标签会加载系统的环境变量，而且环境变量的值会被优先加载，如何查看系统的环境变量?

```java
public static void main(String[] args) throws Exception{
    Map<String, String> env = System.getenv();
    System.out.println(env);
}
```

- 解决方案

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">

    <!--
  	  1. system-properties-mode:设置为NEVER,表示不加载系统属性
  	  2. 避免使用`username`作为属性的`key`
    -->
    <context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
</beans>
```

2. 问题二:当有多个properties配置文件需要被加载，该如何配置?

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
            
    <!--方式一: 可以实现，如果配置文件多的话，每个都需要配置 -->
    <context:property-placeholder location="jdbc.properties,jdbc2.properties" system-properties-mode="NEVER"/>
    
    <!--方式二: *.properties代表所有以properties结尾的文件都会被加载，可以解决方式一的问题，但是不标准-->
    <context:property-placeholder location="*.properties" system-properties-mode="NEVER"/>
    
    <!--方式三: 标准的写法，classpath:代表的是从根路径下开始查找，但是只能查询当前项目的根路径 -->
    <context:property-placeholder location="classpath:*.properties" system-properties-mode="NEVER"/>
    
    <!--方式四: 不仅可以加载当前项目还可以加载当前项目所依赖的所有项目的根路径下的properties配置文件-->
    <context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
</beans>
```


# 7 核心容器


## 7.1 容器的创建方式

1. 类路径下的XML配置文件

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
```

2. 文件系统下的XML配置文件

```java
ApplicationContext ctx = new FileSystemXmlApplicationContext("applicationContext.xml");
```


## 7.2 Bean的三种获取方式

1. `BookDao bookDao = (BookDao) ctx.getBean("bookDao");`
2. `BookDao bookDao = ctx.getBean("bookDao"，BookDao.class);`
3. `BookDao bookDao = ctx.getBean(BookDao.class);`


## 7.3 容器类层次结构

![](http://img.lonely.icu/lonely-md/20220813214159.png)


#### 2.2.4 BeanFactory的使用

-  BeanFactory是延迟加载，只有在获取bean对象的时候才会去创建 
-  ApplicationContext是立即加载，容器加载的时候就会创建bean对象 
-  ApplicationContext要想成为延迟加载，只需要按照如下方式进行配置 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"  lazy-init="true"/>
</beans>
```



# 8总结


## 8.1 容器相关

- BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载
- ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载
- ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展其功能
- ApplicationContext接口常用初始化类 
   - **ClassPathXmlApplicationContext(常用)**
   - FileSystemXmlApplicationContext`


## 8.2 bean相关

![](http://img.lonely.icu/lonely-md/20220813214652.png)


## 8.3 依赖注入相关

![](http://img.lonely.icu/lonely-md/20220813214642.png)
