
# 1. 安装


## 1.1 环境准备

ZooKeeper服务器是用Java创建的，它运行在JVM之上。需要安装JDK 7或更高版本。


## 1.2 安装

1. 
上传文件

2. 
解压
```shell
tar -zxvf apache-ZooKeeper-3.5.6-bin.tar.gz -C /usr/local
```




# 2. 配置启动

1. 
配置zoo.cfg
```shell
# 进入conf目录
/usr/local/apache-zooKeeper-3.5.6-bin/conf/
# 拷贝
cp  zoo_sample.cfg  zoo.cfg

# 修改存储路径
# 创建zooKeeper存储目录
mkdir  zkdata
# 修改zoo.cfg
vim /usr/local/apache-zooKeeper-3.5.6-bin/conf/zoo.cfg
```

<br />![](https://cdn.jsdelivr.net/gh/lonely06/images@main/healthy/202210242025133.png#alt=image-20221024202525023)

2. 
启动zookeeper
```shell
/usr/local/apache-zooKeeper-3.5.6-bin/bin/zkServer.sh  start
```


3. 
查看zookeeper状态
```shell
/usr/local/apache-zooKeeper-3.5.6-bin/bin/zkServer.sh  status
```


4. 
停止zookeeper
```shell
/usr/local/apache-zooKeeper-3.5.6-bin/bin/zkServer.sh  stop
```


