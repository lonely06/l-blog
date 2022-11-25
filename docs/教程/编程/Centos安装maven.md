[安装包](https://wwi.lanzoup.com/iLnro0dyc8fi)

1. 
上传安装包到Linux

2. 
解压
```shell
tar -zxvf apache-maven-3.5.4-bin.tar.gz -C /usr/local
```


3. 
配置环境变量
```shell
vim /etc/profile

#在最后一行追加
export MAVEN_HOME=/usr/local/apache-maven-3.5.4
export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH

# 让配置生效
source /etc/profile
```


4. 
修改maven的settings.xml配置文件
```shell
# 切换目录
cd /usr/local/apache-maven-3.5.4/conf

# 编辑settings.xml
vim settings.xml
	# 增加如下配置
		# 配置本地仓库地址
		<localRepository>/usr/local/repo</localRepository>
		# 在<mirrors>标签中，配置阿里云私服
		<mirror> 
            <id>alimaven</id> 
            <mirrorOf>central</mirrorOf> 
            <name>aliyun maven</name> 
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        </mirror>
```


