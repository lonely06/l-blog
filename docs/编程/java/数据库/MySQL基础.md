
# 数据库相关概念


## 数据库

DataBase(DB)

1. 什么是数据库？

   - 用于存储和管理数据的仓库。
2. 数据库的特点：

   1. 持久化存储数据的。其实数据库就是一个文件系统
   2. 方便存储和管理数据
   3. 使用了统一的方式操作数据库 -- SQL


## 数据库管理系统

DataBase Management System (DBMS)

- 操纵和管理数据库的大型软件


## SQL


### 什么是SQL？

Structured Query Language：结构化查询语言<br />
其实就是定义了操作所有关系型数据库的标准。每一种数据库操作的方式存在不一样的地方，称为“方言”。


### SQL通用语法

1. SQL 语句可以单行或多行书写，以**分号**结尾。
2. 可使用空格和缩进来增强语句的可读性。
3. MySQL 数据库的 SQL 语句不区分大小写，关键字建议使用大写。
4. 注释

   - 单行注释: `-- 注释内容`(推荐使用这种) 或 `#注释内容`(mysql 特有)
   - 多行注释: `/* 注释 */`


### SQL分类


#### DDL

(Data Definition Language)数据定义语言<br />
用来定义数据库对象：数据库，表，列等。关键字：create, drop,alter 等


#### DML

(Data Manipulation Language)数据操作语言<br />
用来对数据库中表的数据进行增删改。关键字：insert, delete, update 等


#### DQL

(Data Query Language)数据查询语言<br />
用来查询数据库中表的记录(数据)。关键字：select, where 等


#### DCL(了解)

(Data Control Language)数据控制语言<br />
用来定义数据库的访问权限和安全级别，及创建用户。关键字：GRANT， REVOKE 等


# MySQL数据库软件


## 数据模型

[[14. redis#NOSQL]]


## 安装


## 卸载

1. 停止MySQL服务
2. 卸载MySQL
3. 删除MySQL安装目录
4. 删除MySQL数据目录

   - 数据存放目录是在 C:\ProgramData\MySQL，直接将该文件夹删除。


## 配置


### 注册MySQL服务

在cmd中输入`mysql -install`


### MySQL服务启动

1. 手动
2. 使用管理员打开cmd（win+R—>cmd—>Ctrl+Shift+Enter）

   - 启动：`net start mysql80`
   - 停止：`net stop mysql80`

      - `mysql80`为mysql的系统服务名


### MySQL登录

1. mysql -u用户名 -p密码
2. mysql -u用户名 -p密码 -h要连接的mysql服务器的ip地址(默认127.0.0.1) -P端口号(默认3306)


### MySQL退出

1. exit
2. quit


### MySQL目录结构

1. MySQL安装目录

   - 配置文件 my.ini
2. MySQL数据目录

   - 数据库：文件夹
   - 表：文件
   - 数据：数据


### MySQL执行日志

- 在mysql配置文件（my.ini）中添加如下配置(重启mysql服务后生效)```
log-output=FILE
general-log=1
general_log_file="D:\mysql.log"
slow-query-log=1
slow_query_log_file="D:\mysql_slow.log"
long_query_time=2
```




# DDL:操作数据库、表


## 操作数据库


### C(Create)：创建

1. 创建数据库：<br />
`create database 数据库名称;`
2. 创建数据库，判断不存在，再创建：<br />
`create database if not exists 数据库名称;`
3. 创建数据库，并指定字符集    (utf8mb4支持4字节)<br />
`create database 数据库名称 character set 字符集名;`


### R(Retrieve)：查询

1. 查询所有数据库的名称<br />
`show databases;`
2. 查询某个数据库的字符集:查询某个数据库的创建语句<br />
`show create database 数据库名称;`


### U(Update)：修改

- 修改数据库的字符集<br />
`alter database 数据库名称 character set 字符集名称;`


### D(Delete)：删除

1. 删除数据库<br />
`drop database 数据库名称;`
2. 判断数据库是否存在，存在则删除<br />
`drop database if exists 数据库名称;`


### 使用数据库

1. 查询当前正在使用的数据库<br />
`select database();`
2. 使用数据库<br />
`use 数据库名称;`


## 操作表


### C(Create)： 创建

1. 创建表：```sql
create table 表名(
                列名1 数据类型1 [comment 列名1注释],
                列名2 数据类型2 [comment 列名2注释],
                ....
                列名n 数据类型n [comment 列名n注释]
            ) [comment 表注释];
            * 注意：最后一列，不需要加逗号(,)
```


   - 数据库类型：

      1. int : 整数类型
      2. double : 小数类型        `score double(5,2)`成绩共5位数，小数点后占2位
      3. char : 定长字符串        `name char(3)`姓名最多为3个字符，不足3个字符也占用3个字符空间；存储性能高，浪费空间
      4. varchar : 变长字符串        `name varchar(20)`姓名最大20个字符；存储性能低，节约空间
      5. date : 日期，只包含年月日，yyyy-MM-dd
      6. datetime : 日期，包含年月日时分秒，yyyy-MM-dd HH:mm:ss
      7. timestamp : 时间戳，包含年月日时分秒，yyyy-MM-dd HH:mm:ss

         - 如果不给该字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值(时间只到2038年)
2. 复制表：<br />
`create table 表名 like 被复制的表名;`


### R(Retrieve)：查询

1. 查询某个数据库中所有的表名称<br />
`show tables;`
2. 查询表结构<br />
`desc 表名;`
3. 查询指定表的建表语句<br />
`show create table 表名;`


### U(Update)：修改

1. 修改表名<br />
`alter table 表名 rename to 新的表名;`
2. 修改表的字符集<br />
`alter table 表名 character set 字符集名称;`
3. 添加一列<br />
`alter table 表名 add 列名 数据类型;`
4. 修改列名称  类型<br />
`alter table 表名 change 列名 新列名 新数据类型;`<br />
`alter table 表名 modify 列名 新数据类型;`
5. 删除列<br />
`alter table 表名 drop 列名;`


### D(Delete)：删除

- 删除表<br />
`drop table 表名;`<br />
`drop table if exists 表名;`
- 删除指定表，并重新创建该表(了解)<br />
`truncate table 表名;`
- 注意：删除表时，表中的全部数据也会被删除


# DML:增删改表中的数据


## 添加数据

- 语法：

   - 给指定列添加数据<br />
`insert into 表名(列名1,列名2,...列名n) values(值1,值2,...值n);`
   - 给全部列添加数据<br />
`insert into 表名 values(值1,值2,...值n);`
   - 批量添加数据<br />
`insert into 表名(列名1,列名2,...列名n) values(值1,值2,...),(值1,值2,...);`<br />
`insert into 表名 values(值1,值2,...),(值1,值2,...);`
- 注意：

   1. 列名和值要一一对应。
   2. 除了数字类型，其他类型需要使用引号(单双都可以)引起来
   3. 插入的数据大小，应在列规定范围内


## 删除数据

- 语法：<br />
`delete from 表名 [where 条件]`
- 注意：

   1. 如果不加条件，则删除表中所有记录。
   2. 如果要删除所有记录

      1. `delete from 表名;` -- 不推荐使用，效率较低。有多少条记录就会执行多少次删除操作。
      2. `truncate table 表名;` -- 推荐使用，效率更高。先删除表，然后再创建一张一样的空表。


## 修改数据

- 
语法：
<br />`update 表名 set 列名1 = 值1, 列名2 = 值2,... [where 条件];`

- 
注意：
<br />如果不加任何条件，则会将表中所有记录全部修改。



# DQL:查询表中的记录


## 语法

```sql
-- 编写顺序                                -- 执行顺序
select                                     from
    字段列表                                	表名列表
from                                       where
    表名列表                                	条件列表
where                                      group by
    条件列表                                	分组字段列表
group by                                   having
    分组字段                                	分组后的条件列表
having                                     select
    分组之后的条件                               字段列表
order by                                   order by
    排序字段                                	排序字段列表
limit                                      limit
    分页参数                                	分页参数
```


## 基础查询

1. 
多个字段的查询
<br />`select 字段名1，字段名2... from 表名;`

   - 
注意：
<br />如果查询所有字段，则可以使用`*`来替代字段列表。

2. 
起别名
<br />`select 字段名 as 别名 from 表名;`

   - as也可以省略
3. 
去除重复
<br />`select distinct 字段列表 from 表名;`

4. 
计算列

   - 一般可以使用四则运算计算一些列的值。（一般只会进行数值型的计算）
   - `ifnull(表达式1,表达式2)`  null参与的运算，计算结果都为null

      - 表达式1：哪个字段需要判断是否为null
      - 表达式2：如果该字段为null后的替换值。


## 条件查询

1. 
where子句后跟条件

2. 
运算符
```sql
* > 、< 、<= 、>= 、= 、<>(表示不等于，也可使用!=，没有==)
* BETWEEN 小值 AND 大值     在某个范围之内(含最小值和最大值)
* IN (列表集合)                 在某个列表中的值，多选一
* LIKE : 模糊查询
	* 占位符：
      * _ : 单个任意字符
      * % : 多个任意字符
* IS NULL
* and 或 &&
* or 或 ||
* not 或 !
```


   - 
代码示例
```sql
-- 年龄大于20岁
SELECT * FROM student WHERE age > 20;
-- 年龄大于等于20岁
SELECT * FROM student WHERE age >= 20;
-- 年龄等于20岁
SELECT * FROM student WHERE age = 20;
-- 年龄不等于20岁
SELECT * FROM student WHERE age <> 20;

-- 年龄大于等于20小于等于30岁
SELECT * FROM student WHERE age >= 20 AND age <= 30;
SELECT * FROM student WHERE age BETWEEN 20 AND 30;

-- 年龄为20或18或22岁
SELECT * FROM student WHERE age = 20 OR age = 18 OR age =22;
SELECT * FROM student WHERE age IN(20, 18, 22);

-- 英语成绩为null
SELECT * FROM student WHERE english IS NULL;

-- 英语成绩不为null
SELECT * FROM student WHERE english IS NOT NULL;

-- 查询姓马的有哪些
SELECT * FROM student WHERE NAME LIKE '马%';

-- 查询姓名第二个字是化的
SELECT * FROM student WHERE NAME LIKE '_化%';

-- 查询姓名是3个字的
SELECT * FROM student WHERE NAME LIKE '___';

-- 查询姓名中包含德的
SELECT * FROM student WHERE NAME LIKE '%德%';
```




## 排序查询

- 
语法：
<br />`select 字段列表 from 表名 order by 排序字段1 排序方式1, 排序字段2 排序方式2...`

- 
排序方式：

   - ASC：升序(默认值)。
   - DESC：降序。
- 
注意：
<br />如果有多个排序条件，则当前边的条件值一样时，才会判断第二条件。



## 聚合函数

将一列数据作为一个整体，进行纵向的计算。

`select 聚合函数(字段列表) from 表名;`

- 
语法：

   1. count：统计个数

      1. 一般选择非空的列：主键
      2. count(*)
   2. max：计算最大值
   3. min：计算最小值
   4. sum：计算和
   5. avg：计算平均值
- 
注意：聚合函数的计算，排除null值。
<br />解决方案：

   1. 选择不包含非空的列进行计算
   2. IFNULL函数


## 分组查询

- 
语法：
<br />`select 字段列表 from 表名 [where 条件] group by 分组字段 [having 分组后过滤条件];`

- 
注意：

   1. 分组之后查询的字段：分组字段、聚合函数，查询其他字段无意义
   2. where 和 having 的区别？

      1. 执行时机不同：where 在**分组之前**进行限定，如果不满足条件，则不参与分组。having在**分组之后**进行限定，如果不满足结果，则不会被查询出来。
      2. 判断条件不同：where后不可以跟聚合函数，having可以进行聚合函数的判断。
   3. 执行顺序：where > 聚合函数 > having


## 分页查询

- 
语法
<br />`select 字段列表 from 表名 limit 开始的索引,每页查询的条数;`

- 
注意

   - 
起始索引从0开始，开始的索引 = (当前的页码 - 1) *  每页显示的条数

   - 
limit 是一个MySQL"方言"，用来完成分页

   - 
如果查询的是第一页数据，开始索引可以省略



# DCL:管理用户，授权(了解)


## 管理用户


### 添加用户

`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`


### 删除用户

`DROP USER '用户名'@'主机名';`


### 修改用户

`alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';`

`UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';`

`SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');`

- mysql中忘记了root用户的密码？

   1. cmd -- > `net stop mysql` 停止mysql服务

      - 需要管理员运行该cmd
   2. 使用无验证方式启动mysql服务： `mysqld --skip-grant-tables`
   3. 打开新的cmd窗口,直接输入`mysql`命令，敲回车。就可以登录成功
   4. `use mysql;`
   5. `update user set password = password('你的新密码') where user = 'root';`
   6. 关闭两个窗口
   7. 打开任务管理器，手动结束mysqld.exe 的进程
   8. 启动mysql服务
   9. 使用新密码登录


### 查询用户

1. 
切换到mysql数据库
<br />`use mysql;`

2. 
查询user表
<br />`SELECT * FROM USER;`


- 注意

   1. 主机名可以用`%`通配
   2. 在MySQL中需要通过用户@主机名的方式，来标识一个用户


## 授权管理


### 查询权限

`SHOW GRANTS FOR '用户名'@'主机名';`


### 授予权限

`grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';`


### 撤销权限

`revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';`


### 权限列表

ALL, ALL PRIVILGES      所有权限

SELECT                  查询数据

INSERT                  插入数据

UPDATE                  修改数据

DELETE                  删除数据

ALTER                   修改表

DROP                    删除数据库/表/视图

CREATE                  创建数据库/表

- 注意

   1. 多个权限之间，使用逗号分隔
   2. 授权时，数据库名和表名可以使用`*`进行统配，代表所有


# 函数


### 字符串函数
| 函数 | 功能 |
| --- | --- |
| concat(S1, S2, ... Sn) | 字符串拼接，将S1，S2，... Sn拼接成一个字符串 |
| lower(str) | 将字符串str全部转为小写 |
| upper(str) | 将字符串str全部转为大写 |
| lpad(str,n,pad) | 左填充，用字符串pad对str的左边进行填充，达到n个字符 串长度 |
| rpad(str,n,pad) | 右填充，用字符串pad对str的右边进行填充，达到n个字符 串长度 |
| trim(str) | 去掉字符串头部和尾部的空格 |
| substring(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串 |



### 数值函数
| 函数 | 功能 |
| --- | --- |
| ceil(x) | 向上取整 |
| floor(x) | 向下取整 |
| mod(x,y) | 返回x/y的模 |
| rand() | 返回0~1内的随机数 |
| round(x,y) | 求参数x的四舍五入的值，保留y位小数 |



### 日期函数
| 函数 | 功能 |
| --- | --- |
| curdate() | 返回当前日期 |
| curtime() | 返回当前时间 |
| now() | 返回当前日期和时间 |
| year(date) | 获取指定date的年份 |
| month(date) | 获取指定date的月份 |
| day(date) | 获取指定date的日期 |
| date_add(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| datediff(date1,date2) | 返回起始时间date1 和 结束时间date2之间的天数 |



### 流程函数
| 函数 | 功能 |
| --- | --- |
| if(value, t, f) | 如果value为true，则返回t，否则返回f |
| ifnull(value1 , value2) | 如果value1不为空，返回value1，否则返回value2 |
| case when [val1] then [res1] ... else [default] end | 如果val1为true，返回res1，... 否则返回default默认值 |
| case [expr] when [val1] then [res1] ... else [default] end | 如果expr的值等于val1，返回res1，... 否则返回default默认值 |



# 约束


## 概念

对表中的数据进行限定，保证数据的正确性、有效性和完整性。


## 分类
| 约束 | 描述 | 关键字 |
| --- | --- | --- |
| 非空约束 | 限制该字段的数据不能为null | NOT NULL |
| 唯一约束 | 保证该字段的所有数据都是唯一、不重复的 | UNIQUE |
| 主键约束 | 主键是一行数据的唯一标识，要求非空且唯一 | PRIMARY KEY |
| 默认约束 | 保存数据时，如果未指定该字段的值，则采用默认值 | DEFAULT |
| 检查约束 | 保证字段值满足某一个条件  (8.0.16版本之后) | CHECK |
| 外键约束 | 用来让两张表的数据之间建立连接，保证数据的一致 性和完整性 | FOREIGN KEY |


- 注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。


## 非空约束

not null，某一列的值不能为null

1. 
创建表时添加约束
```sql
CREATE TABLE stu(
    id INT,
    NAME VARCHAR(20) NOT NULL -- name为非空
);
```


2. 
创建表完后，添加非空约束
<br />`ALTER TABLE stu MODIFY NAME VARCHAR(20) NOT NULL;`

3. 
删除name的非空约束
<br />`ALTER TABLE stu MODIFY NAME VARCHAR(20);`



## 唯一约束

unique，某一列的值不能重复

1. 
注意
<br />唯一约束可以有NULL值，但是只能有一条记录为null

2. 
在创建表时，添加唯一约束
```sql
CREATE TABLE stu(
		id INT,
	  phone_number VARCHAR(20) UNIQUE -- 手机号
);
```


3. 
删除唯一约束
<br />`ALTER TABLE stu DROP INDEX phone_number;`

4. 
在表创建完后，添加唯一约束
<br />`ALTER TABLE stu MODIFY phone_number VARCHAR(20) UNIQUE;`



## 主键约束

primary key

1. 
注意

   1. 含义：非空且唯一
   2. 一张表只能有一个字段为主键
   3. 主键就是表中记录的唯一标识
2. 
在创建表时，添加主键约束
```sql
create table stu(
    id int primary key,-- 给id添加主键约束
    name varchar(20)
);
```


3. 
删除主键
```sql
-- 错误 alter table stu modify id int ;
ALTER TABLE stu DROP PRIMARY KEY;
```


4. 
创建完表后，添加主键
<br />`ALTER TABLE stu MODIFY id INT PRIMARY KEY;`

5. 
自动增长

   1. 
概念：如果某一列是数值类型的，使用`auto_increment`可以来完成值得自动增长。

   2. 
在创建表时，添加主键约束，并且完成主键自增长
```sql
create table stu(
    id int primary key auto_increment,-- 给id添加主键约束
    name varchar(20)
);
```


   3. 
删除自动增长
<br />`ALTER TABLE stu MODIFY id INT;`

   4. 
添加自动增长
<br />`ALTER TABLE stu MODIFY id INT AUTO_INCREMENT;`



## 外键约束

foreign key,让表与表产生关系，从而保证数据的正确性。

1. 
在创建表时，可以添加外键
```sql
create table 表名(
    ....
    外键列
    constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)
);
```


2. 
删除外键
<br />`ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;`

3. 
创建表之后，添加外键
<br />`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称);`

4. 
级联操作（谨慎使用）

   1. 
添加级联操作
<br />`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称) ON UPDATE CASCADE ON DELETE CASCADE;`

   2. 
分类
| 行为 | 说明 |
| --- | --- |
| no action | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| restrict | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| casade | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| set null | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| set default | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持) |





# 多表查询


## 多表关系

- 一对多（多对一）

   - 实现: 在多的一方建立外键，指向一的一方的主键
- 多对多

   - 实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键
- 一对一

   - 关系：一对一关系，多用于单表拆分，以提升操作效率
   - 实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)


## 查询语法

```sql
select
    列名列表
from
    表名列表
where....
```


## 笛卡尔积

- 有两个集合A,B .取这两个集合的所有组成情况。
- 要完成多表查询，需要消除无用的数据。


## 多表查询的分类：


### 内连接查询

查询两张表的交集部分

1. 
隐式内连接：使用where消除无用条件

   - 
语法：`select 字段列表 from 表1, 表2 where 条件`

   - 
代码示例

```sql
-- 查询所有员工信息和对应的部门信息
SELECT * FROM emp, dept WHERE emp.`dept_id` = dept.`id`;
```

2. 
显式内连接：
<br />语法：`select 字段列表 from 表名1 [inner] join 表名2 on 条件`

   - 
代码示例
```sql
SELECT * FROM emp INNER JOIN dept ON emp.`dept_id` = dept.`id`;

SELECT * FROM emp t1 JOIN dept t2 ON t1.`dept_id` = t2.`id`;
```


3. 
内连接查询：

   1. 从哪些表中查询数据
   2. 条件是什么
   3. 查询哪些字段
4. 
注意：如果为表名起了别名之后，则不能在使用表名来指定对应的字段，只能够使用别名来指定字段。



### 外连接查询

1. 
左外连接（使用更多）
<br />语法：`select 字段列表 from 表1 left [outer] join 表2 on 条件;`
<br />查询的是左表所有数据以及其两表交集部分。

2. 
右外连接
<br />语法：`select 字段列表 from 表1 right [outer] join 表2 on 条件;`
<br />查询的是右表所有数据以及其两表交集部分。



### 自连接

1. 
语法：`select 字段列表 from 表A 别名A join 表B 别名B on 条件;`

2. 
注意：

   - 自连接查询，可以是内连接查询，也可以是外连接查询。
   - 自连接查询中，必须要为表起别名


### 联合查询

1. 
语法

   1. 
union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

   2. 
union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。
```sql
select 字段列表 from 表A
union [all]
select 字段列表 from 表B;
```


2. 
注意：对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。查询字段不同时，会报错。



### 子查询

- 
概念：查询中嵌套查询，称嵌套查询为子查询。

   - 
代码实例
```sql
-- 查询员工表中工资金额最高的员工信息
SELECT * FROM emp WHERE emp.`salary` = (SELECT MAX(emp.`salary`) FROM emp);
```


- 
子查询不同情况

   1. 
标量子查询(子查询的结果是单行单列的)：

      - 
子查询可以作为条件，使用运算符去判断。 运算符： `> >= < <= = <>`

      - 
代码实例
```sql
-- 查询员工工资小于平均工资的人
SELECT * FROM emp WHERE emp.`salary` < (SELECT AVG(emp.`salary`) FROM emp);
```


   2. 
列子查询(子查询的结果是多行单列的)：

      - 
常用操作符
| 操作符 | 描述 |
| --- | --- |
| in | 在指定的集合范围之内，多选一 |
| not in | 不在指定的集合范围之内 |
| any | 子查询返回列表中，有任意一个满足即可 |
| some | 与ANY等同，使用SOME的地方都可以使用ANY |
| all | 子查询返回列表的所有值都必须满足 |



      - 
代码实例
```sql
-- 查询'财务部'和'市场部'所有的员工信息
SELECT * FROM emp WHERE emp.`dept_id` IN (SELECT id FROM dept WHERE dept.`NAME`='财务部' OR dept.`NAME`='市场部');
```


   3. 
行子查询(子查询结果为一行多列)

      - 
常用操作符：`=, <>, in, not in`

      - 
代码实列
```sql
-- 查询与 "张无忌" 的薪资及直属领导相同的员工信息
select * from emp where (salary, managerid) = (select salary, managerid from emp where name = '张无忌');
```


   4. 
表子查询(子查询的结果是多行多列的)：

      - 
子查询可以作为一张虚拟表参与查询

      - 
常用操作符：in

      - 
代码实例
```sql
-- 查询员工入职日期是2011-11-11之后的员工信息和部门信息
-- 子查询
SELECT * FROM dept t1, (SELECT * FROM emp WHERE emp.`join_date` > '2011-11-11') t2
WHERE t1.`id` = t2.dept_id;
-- 普通内连接
SELECT * FROM dept t1, emp t2 WHERE t1.`id` = t2.`dept_id` AND t2.`join_date` > '2011-11-11';
```




# 事务


## 事务概念

如果一个包含多个步骤的业务操作，被事务管理，那么这些操作要么同时成功，要么同时失败。


## 事务操作

1. 开启事务：`start transaction 或 begin;`
2. 回滚：`rollback;`
3. 提交：`commit;`
4. MySQL数据库中事务默认自动提交

   - 事务提交的两种方式：

      - 自动提交

         - mysql就是自动提交的
         - 一条DML(增删改)语句会自动提交一次事务。
      - 手动提交

         - Oracle 数据库默认是手动提交事务
         - 需要先开启事务，再提交
   - 修改事务的默认提交方式：

      - 查看事务的默认提交方式：`SELECT @@autocommit;` -- 1 代表自动提交  0 代表手动提交
      - 修改默认提交方式： `set @@autocommit = 0;`


## 事务的4大特性(ACID)


### 原子性(Atomicity)

事务是不可分割的最小操作单位，要么同时成功，要么同时失败。


### 持久性(Durability)

当事务提交或回滚后，数据库会持久化的保存数据。


### 隔离性(Isolation)

多个事务之间相互独立。


### 一致性(Consistency)

事务操作前后，数据总量不变


## 并发事务存在问题

1. 脏读：一个事务读取到另一个事务中没有提交的数据。
2. 不可重复读(虚读)：在同一个事务中，两次读取到的数据不一样。
3. 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据 已经存在，好像出现了 "幻影"。


## 事务的隔离级别(了解)


### 概念

多个事务之间隔离的，相互独立的。但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别就可以解决这些问题。


### 隔离级别

1. read uncommitted：读未提交

   - 产生的问题：脏读、不可重复读、幻读
2. read committed：读已提交 （Oracle默认）

   - 产生的问题：不可重复读、幻读
3. repeatable read：可重复读 （MySQL默认）

   - 产生的问题：幻读
4. serializable：串行化

   - 可以解决所有的问题

- 注意：隔离级别从小到大安全性越来越高，但是效率越来越低

- 
数据库查询隔离级别：
<br />`select @@transaction_isolation;`

- 
数据库设置隔离级别：
<br />`set [session|global] transaction isolation level 级别字符串;`

   - session：当前会话
   - global：全部会话


# 数据库的设计


## 多表之间的关系


### 分类

1. 一对一（了解）
2. 一对多（多对一）
3. 多对多


### 实现关系

1. 一对一

   - 一张表即可
   - 实现方式：可以在任意一方添加唯一外键指向另一方的主键。
2. 一对多

   - 实现方式：在多的一方建立外键，指向一的一方的主键。
3. 多对多

   - 实现方式：需要借助第三张中间表。中间表至少包含两个字段，这两个字段作为第三张表的外键，分别指向两张表的主键。


## 数据库设计的范式

- 
概念：设计数据库时，需要遵循的一些规范。要遵循后边的范式要求，必须先遵循前边的所有范式要求。

   - 
六种范式
<br />设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。
<br />目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。

- 
分类

   1. 第一范式（1NF）：每一列都是不可分割的原子数据项。
   2. 第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主码的部分函数依赖）。

      - 几个概念：

         1. 函数依赖：A-->B,如果通过A属性(属性组)的值，可以确定唯一B属性的值。则称B依赖于A。
         2. 完全函数依赖：A-->B，如果A是一个属性组，则B属性值的确定需要依赖于A属性组中所有的属性值。
         3. 部分函数依赖：A-->B，如果A是一个属性组，则B属性值的确定只需要依赖于A属性组中某一些值即可。
         4. 传递函数依赖：A-->B, B -- >C，如果通过A属性(属性组)的值，可以确定唯一B属性的值，在通过B属性（属性组）的值可以确定唯一C属性的值，则称 C 传递函数依赖于A。
         5. 码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性(属性组)为该表的码。
   3. 第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）。


# 数据库的备份和还原


## 命令行

- 语法

   - 备份：`mysqldump -u用户名 -p密码 数据库名称 > 保存的路径`
   - 还原：

      1. 登录数据库
      2. 创建数据库
      3. 使用数据库
      4. 执行文件        `source 文件路径`


## 图形化工具
