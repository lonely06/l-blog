[安装包](https://wwi.lanzoup.com/iB2bF0dxi7eb)

1. 
上传安装包到Linux

2. 
解压压缩包到/usr/local下
```shell
tar -zxvf apache-tomcat-7.0.57.tar.gz -C /usr/local
```


3. 
启动tomcat
```shell
# 进入tomcat的bin目录
cd /usr/local/apache-tomcat-7.0.57/bin
sh startup.sh 或 ./startup.sh
```



- 
查看tomcat日志信息
```shell
more /usr/local/apache-tomcat-7.0.57/logs/catalina.out
```


- 
查看进程
```shell
ps -ef | grep tomcat
```


- 
需开启8080端口
```shell
# 开放8080端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
# 重新加载防火墙
firewall-cmd --reload
```


- 
关闭tomcat

   - 通过bin目录下的shutdown.sh
   - kill -9 进程id
