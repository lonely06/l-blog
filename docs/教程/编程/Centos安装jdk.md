
## 1. 在user目录下新建java文件夹
```shell
# cd /usr/
# mkdir java
# cd java
```

##  2. 下载jdk1.8，[jdk-8u341-linux-x64.tar.gz](https://www.oracle.com/java/technologies/downloads/#java8)，到java文件夹下

##  3. 使用`tar -zxvf 文件名`进行解压

##  4. 配置环境变量
```shell
vim /etc/profile
```
在最前面添加：
```shell
export JAVA_HOME=/usr/java/jdk1.8.0_341
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```
执行：
```shell
source /etc/profile
```
可使配置立即生效

##  5. 检测jdk是否安装成功
```shell
java -version
```
