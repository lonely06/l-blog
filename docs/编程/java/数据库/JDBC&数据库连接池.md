
## JDBC


### 1.概念

**Java DataBase Connectivity**  Java 数据库连接， Java语言操作数据库

- JDBC本质：官方（sun公司）定义的一种操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。


### 2.入门

- 步骤： 
   1. 导入驱动jar包 
      1. 复制jar包到项目的libs目录下
      2. 右键-->Add As Library
   2. 注册驱动
   3. 获取数据库链接对象  Commection
   4. 定义sql
   5. 获取执行sql语句的对象  Statement
   6. 执行sql，接收返回结果
   7. 处理结果
   8. 释放资源


### 3.详解各个对象

1.  DriverManager：驱动管理对象 
   -  功能 
      1.  注册驱动：告诉程序该使用哪一个数据库驱动jar包 
         -  代码实现：`Class.forName("com.jdbc.mysql.Driver")` 
         -  注意：mysql 5之后的驱动jar包可以省略注册驱动的步骤（但不建议省略） 
      2.  获取数据库链接： 
         - 方法：`static Connection getConnection(String url, String user, String password)`
         - 参数： 
            - url：指定连接的路径 
               - 语法：`jdbc:mysql://ip地址(域名):端口/数据库名称`
               - 细节：如果连接的是本机mysql服务器，并且mysql服务器默认端口是3306，则url可以简写为：`jdbc:mysql:///数据库名称`
            - user：用户名
            - password：密码
2.  Connection：数据库连接对象 
   - 功能： 
      1. 获取执行sql对象(Statement) 
         - `Statement createStatement()`
         - `PreparedStatement prepareStatement(String sql)`
      2. 管理事务 
         - 开启事务：`setAutoCommit(boolean autoCommit)` ：调用该方法设置参数为false，即开启事务
         - 提交事务：`commit()`
         - 回滚事务：`rollback()`
3.  Statement：执行sql的对象 
   - 执行sql方法 
      1. `boolean execute(String sql)` ：可以执行任意的sql（了解）
      2. `int executeUpdate(String sql)` ：执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句 
         - 返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功，返回值>0则执行成功，反之，则失败。
      3. `ResultSet executeQuery(String sql)` ：执行DQL（select)语句
4.  ResultSet：结果集对象，封装查询结果 
   -  方法 
      1. `boolean next()`: 游标向下移动一行，判断当前行是否是最后一行末尾(是否有数据)，如果是，则返回false，如果不是则返回true
      2. `getXxx(参数)`:获取数据 
         - Xxx：代表数据类型。例：int getInt();	string getString()
         - 参数： 
            1. int：代表列的编号，从1开始。例：getString(1)
            2. String：代表列名称。例：getDouble("balance")
   -  注意 
      -  使用步骤 
         1. 游标向下移动一行
         2. 判断是否有数据
         3. 获取数据
      -  代码实例 
```java
//循环判断游标是否是最后一行末尾
while(rs.next()){
    //获取数据
	int id = rs.getInt(1);
    String name = rs.getString("String");
    double balance = rs.getDouble("balance");
    System.out.println(id + "---" + name + "---" + balance);
}
```
 

5.  PrepareStatement：执行sql的对象 
   1. sql注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题。 
      1. 用户名随便输入，输入密码：a' or 'a' = 'a'
      2. sql：select * from user where username = 'fhdsjkf' and password = 'a' or 'a' = 'a'
   2. 解决sql注入问题：使用PrepareStatement对象来解决
   3. 预编译的sql：参数使用?作为占位符
   4. 步骤: 
      1. 导入驱动jar包
      2. 注册驱动
      3. 获取数据库链接对象  Connection
      4. 定义sql 
         - 注意：sql的参数使用?作为占位符。例：`select * from user where name = ? and password = ?;`
      5. 获取执行sql语句的对象。  `PrepareStatement-->Connection.prepareStatement(String sql)`
      6. 给?赋值 
         - 方法：`setXxx(参数1, 参数2)` 
            - 参数1：?的位置编号，从1开始
            - 参数2：?的值
      7. 执行sql，接收返回结果，不需要传递sql语句
      8. 处理结果
      9. 释放资源
   5. 注意：后期都会使用PrepareStatement来完成增删改查的所有操作 
      1. 可以防止sql注入
      2. 效率更高


## 抽取JDBC工具列：JDBCUtils


### 1.目的

简化书写


### 2.分析

1.  抽取注册驱动 
2.  抽取数据库链接对象 
   -  需求：不传递参数，同时保证工具类的通用性。 
   -  解决：通过配置文件<br />创建文件	jdbc.properties<br />文件中的内容	url=数据库路径<br />			   user=用户名<br />			   password=密码<br />			   driver=驱动jar包 
3.  抽取释放资源 


### 3.代码实现

```java
import java.io.FileReader;
import java.io.IOException;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

/**
 * JDBC工具类
 */

public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;

    /**
     *  使用静态代码块，读取一次拿到文件中的值
     */
    static {
        //创建Propertise集合类
        Properties pro = new Properties();

        //通过ClassLoader类加载器，获取src下的文件路径
        ClassLoader classLoader = JDBCUtils.class.getClassLoader();
        URL res = classLoader.getResource("jdbc.properties");
        String path = res.getPath();

        //加载文件
        try {
            pro.load(new FileReader(path));
        } catch (IOException e) {
            e.printStackTrace();
        }

        //获取文件中的数据
        url = pro.getProperty("url");
        user = pro.getProperty("user");
        password = pro.getProperty("password");
        driver = pro.getProperty("driver");

        //注册驱动
        try {
            Class.forName(driver);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取数据库链接对象
     *
     * @return
     */
    public static Connection getConnecton() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    /**
     * 释放资源
     *
     * @param conn
     * @param stat
     */
    public static void close(Connection conn, Statement stat) {
        if (stat != null) {
            try {
                stat.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }

    /**
     * 释放资源
     *
     * @param conn
     * @param stat
     */
    public static void close(Connection conn, Statement stat, ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (stat != null) {
            try {
                stat.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```


## JDBC控制事务


### 1.事务

一个包含多个步骤的业务的操作。如果这个业务操作被事务管理，则这多个步骤要么同时成功，要么同时失败。


### 2.操作

1. 开启事务
2. 提交事务
3. 回滚事务


### 3.使用Connection对象来管理事务

-  开启事务	`setAutoCommit(boolean autoCommit)` ：调用该方法设置参数为false，即开启事务 
   - 在执行sql之前开启事务
-  提交事务	`commit()` 
   - 当所有sql都执行完提交事务
-  回滚事务	`rollback()` 
   - 在catch中回滚事务


### 4.代码实现

```java
import com.it.utils.JDBCUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
/**
 * 张三向李四转账500
 */
public class JDBCDemo12 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement pstat1 = null;
        PreparedStatement pstat2 = null;

       try {
           //获取数据库链接对象
           conn = JDBCUtils.getConnecton();
           //开启事务
           conn.setAutoCommit(false);
           //创建sql
           String sql1 = "update account set balance = balance - ? where id = ?";
           String sql2 = "update account set balance = balance + ? where id = ?";
           //获取执行sql对象
           pstat1 = conn.prepareStatement(sql1);
           pstat2 = conn.prepareStatement(sql2);
           //设置参数
           pstat1.setInt(1, 500);
           pstat1.setInt(2, 1);

           pstat2.setInt(1, 500);
           pstat2.setInt(2, 2);
           //执行sql
           pstat1.executeUpdate();
           //手动制造错误
//           int i = 3/0;
           pstat2.executeUpdate();

           //提交事务
           conn.commit();
       } catch (Exception throwables) {
           //回滚事务
           try {
               if(conn != null) {
                   conn.rollback();
               }
           } catch (SQLException e) {
               e.printStackTrace();
           }
           throwables.printStackTrace();
        } finally {
           //释放资源
           JDBCUtils.close(null, pstat2);
           JDBCUtils.close(null, pstat1);

       }
    }
}
```


## 数据库链接池

### 1.概念
就是一个容器（集合），存放数据库连接的容器。<br />当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

### 2. 好处

1. 节约资源
2. 提高系统响应速度
3. 避免数据库连接遗漏

### 3. 实现

1. 标准接口：**DataSource**	javax.sql包下
   - 官方提供的数据库连接池标准接口，由第三方组织实现此接口
   - 方法： 
      - 获取连接：`getConnection()`
      - 归还连接：`Connection.close()`  如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接，而是归还连接
2. 一般不由我们自己实现，而是数据库厂商来实现 
   1. C3P0：数据库连接池技术
   2. Druid：数据库连接池实现技术，由阿里巴巴提供

### 4. C3P0（了解）

-  步骤： 
   1.  导入jar包 
      - 不要忘记导入数据驱动jar包
   2.  定义配置文件： 
      - 名称：cep0.properties 或者 c3p0-config.xml
      - 路径：直接将文件方法src目录下即可
   3.  创建核心对象：数据库连接池对象  ComboPooledDataSource 
   4.  获取连接：getConnection 
-  代码实现： 
```java
//1.创建数据库连接池对象
DataSource ds  = new ComboPooledDataSource();
//2. 获取连接对象
Connection conn = ds.getConnection();
```
 

### 5.Druid

-  步骤： 
   1. 导入jar包
   2. 定义配置文件： 
      - 是properties形式的
      - 可以叫任意名称，可以放在任意目录下
   3. 加载配置文件：Properties
   4. 获取数据库连接池对象：通过工厂类来获取  DruidDataSourceFactory
   5. 获取链接：getConnection
-  代码实现： 
```java
//1.加载配置文件
Properties pro = new Properties();
InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
pro.load(is);
//2.获取连接池对象
DataSource ds = DruidDataSourceFactory.createDataSource(pro);
//3.获取连接
Connection conn = ds.getConnection();
```
 

-  定义工具类 
   1. 定义一个工具类  JDBCUtils
   2. 提供静态代码块加载配置文件，初始化连接池对象
   3. 提供方法 
      1. 获取链接方法：通过数据库连接池获取连接
      2. 释放资源方法
      3. 获取连接池方法
-  代码实现： 
```java
public class JDBCUtils {
    private static DataSource ds;
    //静态代码块加载配置文件，获取链接池对象
    static {
        try {
            Properties pro = new Properties();
 			 pro.load(DruidUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //获取连接对象
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }
    
    //获取连接池对象
    public static DataSource getDataSource(){
        return ds;
    }

    //释放资源
    public static void close(Statement stat, Connection conn){
        close(null, stat, conn);
    }

    public static void close(ResultSet rs, Statement stat, Connection conn){
        if(rs != null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if(stat != null){
            try {
                stat.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if(conn != null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

## Spring JDBC

-  Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发 
-  步骤： 
   1.  导入jar包 
   2.  创建JdbcTemplate对象。依赖于数据源DataSource<br />`JdbcTemplate template = new JdbcTemplate(ds);` 
   3.  调用JdbcTemplate的方法来完成CRUD的操作 
      -  `update()`：执行DML语句。增、删、改语句 
      -  `queryForMap()`：查询结果将结果封装为Map集合，接列名作为key，将值作为value，将这条记录封装为一个map集合 
         - 注意：这个方法查询的结果集长度只能是1
      -  `queryForList()`：查询结果将结果封装为list集合 
         - 注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
      -  `query()`：查询结果，将结果封装为JavaBean对象 
         - query的参数：RowMapper 
            - 一般我们使用BeanPropertyRowMapper实现类。可以完成数据列到JavaBean的自动封装
            - `new BeanPropertyRowMapper<类型>(类型.class)`
      -  `queryForObject()`：查询结果，将结果封装为对象 
         - 一般用于聚合函数的查询
