-  注意：centos中自带的mariadb数据库与mysql冲突，需要先卸载mariadb 
```shell
# 查看系统中是否有mysql和mariadb
rpm -qa | grep mysql
rpm -qa | grep mariadb

# 卸载对应的软件
rpm -e --nodeps  软件名称

# 开放端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```



## 1. 下载

[https://downloads.mysql.com/archives/community/](https://downloads.mysql.com/archives/community/)

![](http://img.lonely.icu/lonely-md/202210160834502.png)


## 2. mysql-8.0.28 安装

1.  上传mysql-8.0.28-1.el7.x86_64.rpm-bundle.tar 
2.  解压 
```shell
tar -xvf mysql-8.0.28-1.el7.x86_64.rpm-bundle.tar
```


3.  安装 
```shell
rpm -ivh mysql-community-common-8.0.28-1.el7.x86_64.rpm 

rpm -ivh mysql-community-client-plugins-8.0.28-1.el7.x86_64.rpm 

rpm -ivh mysql-community-libs-8.0.28-1.el7.x86_64.rpm 

rpm -ivh mysql-community-libs-compat-8.0.28-1.el7.x86_64.rpm

yum install openssl-devel

rpm -ivh  mysql-community-devel-8.0.28-1.el7.x86_64.rpm

rpm -ivh mysql-community-client-8.0.28-1.el7.x86_64.rpm

yum install net-tools

rpm -ivh mysql-community-icu-data-files-8.0.28-1.el7.x86_64.rpm

rpm -ivh  mysql-community-server-8.0.28-1.el7.x86_64.rpm
```


4.  启动服务 
```shell
# 查询服务状态
systemctl status mysqld

# 启动服务
systemctl start mysqld

# 重启服务
systemctl restart mysqld

# 停止服务
systemctl stop mysqld

# 设置开机自启
systemctl enable mysqld
```


5.  查询mysql临时密码 
```shell
cat /var/log/mysqld.log | grep password
```


6.  登录/修改密码 
```shell
# 登录mysql
mysql -uroot -p

# 修改初始密码
ALTER  USER  'root'@'localhost'  IDENTIFIED BY 'Abc123...';

# 降低密码等级
set global validate_password.policy = 0;
set global validate_password.length = 4;

# 修改密码
ALTER  USER  'root'@'localhost'  IDENTIFIED BY '1234';

# 创建用户，允许远程访问
create user 'root'@'%' IDENTIFIED WITH mysql_native_password BY '1234';

# 授予权限
grant all on *.* to 'root'@'%';

# 退出
exit

# 重新连接
```



## 3. mysql-5.7.25 安装

1.  上传mysql-5.7.25-1.el7.x86_64.rpm-bundle.tar 
2.  解压 
```shell
tar -xvf mysql-5.7.25-1.el7.x86_64.rpm-bundle.tar
```


3.  安装 
```shell
rpm -ivh mysql-community-common-5.7.25-1.el7.x86_64.rpm

rpm -ivh mysql-community-libs-5.7.25-1.el7.x86_64.rpm

rpm -ivh mysql-community-devel-5.7.25-1.el7.x86_64.rpm

rpm -ivh mysql-community-libs-compat-5.7.25-1.el7.x86_64.rpm

rpm -ivh mysql-community-client-5.7.25-1.el7.x86_64.rpm

yum install net-tools

rpm -ivh mysql-community-server-5.7.25-1.el7.x86_64.rpm
```


4.  启动服务 
```shell
# 查询服务状态
systemctl status mysqld

# 启动服务
systemctl start mysqld

# 重启服务
systemctl restart mysqld

# 停止服务
systemctl stop mysqld

# 设置开机自启
systemctl enable mysqld
```


5.  查询mysql临时密码 
```shell
cat /var/log/mysqld.log | grep password
```


6.  登录/修改密码 
```shell
# 登录mysql
mysql -uroot -p

# 设置密码长度最低位数
set global validate_password_length=4;
# 设置密码安全等级低
set global validate_password_policy=LOW;

# 修改密码
set password = password('root');

# 开启访问权限
grant all on *.* to 'root'@'%' identified by 'root';
flush privileges;

# 退出
exit

# 重新连接
```


-  注意： 
   -  当出现连接失败时，可尝试在链接后添加useSSL=false参数 
```shell
jdbc:mysql://192.168.19.100:3306?useSSL=false
```

