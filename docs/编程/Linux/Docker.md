# 1.初识Docker

## 1.1.什么是Docker

### 1.1.1.Docker解决依赖兼容问题

Docker解决依赖的兼容问题，采用了两个手段：

- 将应用的Libs（函数库）、Deps（依赖）、配置与应用一起打包

- 使用沙箱机制,将每个应用放到一个隔离**容器**去运行，避免互相干扰

![image-20210731142219735](http://img.lonely.icu//lonely-md/202212301958453.png)

### 1.1.2.Docker解决操作系统环境差异

- Docker将用户程序与所需要调用的系统(比如Ubuntu)函数库一起打包
- Docker运行到不同操作系统时，直接基于打包的函数库，借助于操作系统的Linux内核来运行

![image-20210731144820638](http://img.lonely.icu//lonely-md/202212301958128.png)

### 1.1.3.总结

Docker是一个快速交付应用、运行应用的技术，具备下列优势：

- 可以将程序及其依赖、运行环境一起打包为一个镜像，可以迁移到任意Linux操作系统
- 运行时利用沙箱机制形成隔离容器，各个应用互不干扰
- 启动、移除都可以通过一行命令完成，方便快捷

## 1.2.Docker架构

### 1.2.1.镜像和容器

- **镜像（Image）**：Docker将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像。

- **容器（Container）**：镜像中的应用程序运行后形成的进程就是**容器**，只是Docker会给容器进程做隔离，对外不可见。

而**镜像**，就是把一个应用在硬盘上的文件、及其运行环境、部分系统函数库文件一起打包形成的文件包。这个文件包是只读的。

**容器**呢，就是将这些文件中编写的程序、函数加载到内存中允许，形成进程，只不过要隔离起来。因此一个镜像可以启动多次，形成多个容器进程。

### 1.2.2.DockerHub

- DockerHub：[DockerHub](https://hub.docker.com/)是一个官方的Docker镜像的托管平台。这样的平台称为Docker Registry。

- 国内也有类似于DockerHub 的公开服务，比如 [网易云镜像服务](https://c.163yun.com/hub)、[阿里云镜像库](https://cr.console.aliyun.com/)等。

### 1.2.3.Dockr架构

Docker是一个CS架构的程序，由两部分组成：

- 服务端(server)：Docker守护进程，负责处理Docker指令，管理镜像、容器等

- 客户端(client)：通过命令或RestAPI向Docker服务端发送指令。可以在本地或远程向服务端发送指令。

![image-20210731154257653](http://img.lonely.icu//lonely-md/202212302003962.png)

# 2.Docker的基本操作

## 2.1.镜像操作

### 2.1.1.镜像名称

- 镜名称一般分两部分组成：[repository]:[tag]。
- 在没有指定tag时，默认是latest，代表最新版本的镜像

### 2.1.2.镜像命令

常见的镜像操作命令如图：

- 利用docker xx --help命令查看某个命令的具体用法

![image-20210731155649535](http://img.lonely.icu//lonely-md/202212302006556.png)



## 2.2.容器操作

### 2.2.1.容器相关命令

![image-20210731161950495](http://img.lonely.icu//lonely-md/202212302006834.png)

- 容器保护三个状态：

  - 运行：进程正常运行

  - 暂停：进程暂停，CPU不再运行，并不释放内存

  - 停止：进程终止，回收进程占用的内存、CPU等资源


- 运行容器：`docker run`

  - 常见参数：

    - --name：指定容器名称
    - -p：指定端口映射，冒号左侧是宿主机端口，右侧是容器端口
    - -v：挂载一个数据卷到某个容器内目录
    - -d：容器后台运行

- 暂停容器：`docker pause`

- 恢复运行：`docker unpause`

- 停止运行：`docker stop`

- 再次运行：`docker start`

- 删除容器：`docker rm`

- 进入容器：`docker exec`

  - 命令解读

    ```sh
    docker exec -it mn bash
    ```

    - docker exec ：进入容器内部，执行一个命令

    - -it : 给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互

    - mn ：要进入的容器的名称

    - bash：进入容器后执行的命令，bash是一个linux终端交互命令

- 查看日志：`docker logs`

  - 添加 -f 参数可以持续查看日志

- 查看运行状态：`docker ps`

  - `docker ps -a`查看所有容器，包括已经停止的


## 2.3.数据卷（容器数据管理）

### 2.3.1.什么是数据卷

**数据卷（volume）**是一个虚拟目录，指向宿主机文件系统中的某个目录。

![image-20210731173541846](http://img.lonely.icu//lonely-md/202212302006075.png)

一旦完成数据卷挂载，对容器的一切操作都会作用在数据卷对应的宿主机目录。

数据卷的作用：

- 将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全

数据卷操作：

- docker volume create：创建数据卷
- docker volume ls：查看所有数据卷
- docker volume inspect：查看数据卷详细信息，包括关联的宿主机目录位置
- docker volume rm：删除指定数据卷
- docker volume prune：删除所有未使用的数据卷

### 2.3.2.挂载数据卷

在创建容器时，可以通过 -v 参数来挂载一个数据卷到某个容器内目录，命令格式如下：

```sh
docker run \
  --name mn \
  -v html:/root/html \
  -p 8080:80
  nginx \
# -v html:/root/htm 把html数据卷挂载到容器内的/root/html这个目录中
```

# 3.Dockerfile自定义镜像

## 3.1.镜像结构

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成。

![image-20210731175806273](http://img.lonely.icu//lonely-md/202212302031878.png)

## 3.2.Dockerfile语法

![image-20210731180321133](http://img.lonely.icu//lonely-md/202212302031755.png)

更新详细语法说明，请参考官网文档： https://docs.docker.com/engine/reference/builder

## 3.3.构建Java项目

### 3.3.1.基于Ubuntu构建Java项目

需求：基于Ubuntu镜像构建一个新镜像，运行一个java项目

- 步骤1：新建一个空文件夹docker-demo

  ![image-20210801101207444](http://img.lonely.icu//lonely-md/202212302031386.png)

- 步骤2：拷贝docker-demo.jar文件到docker-demo这个目录

  ![image-20210801101314816](http://img.lonely.icu//lonely-md/202212302031997.png)

- 步骤3：拷贝jdk8.tar.gz文件到docker-demo这个目录

  ![image-20210801101410200](http://img.lonely.icu//lonely-md/202212302031373.png)

- 步骤4：拷贝Dockerfile到docker-demo这个目录

  ![image-20210801101455590](http://img.lonely.icu//lonely-md/202212302031235.png)

  Dockerfile内容：

  ```dockerfile
  # 指定基础镜像
  FROM ubuntu:16.04
  # 配置环境变量，JDK的安装目录
  ENV JAVA_DIR=/usr/local
  
  # 拷贝jdk和java项目的包
  COPY ./jdk8.tar.gz $JAVA_DIR/
  COPY ./docker-demo.jar /tmp/app.jar
  
  # 安装JDK
  RUN cd $JAVA_DIR \
   && tar -xf ./jdk8.tar.gz \
   && mv ./jdk1.8.0_144 ./java8
  
  # 配置环境变量
  ENV JAVA_HOME=$JAVA_DIR/java8
  ENV PATH=$PATH:$JAVA_HOME/bin
  
  # 暴露端口
  EXPOSE 8090
  # 入口，java项目的启动命令
  ENTRYPOINT java -jar /tmp/app.jar
  ```

- 步骤5：进入docker-demo

  将准备好的docker-demo上传到虚拟机任意目录，然后进入docker-demo目录下

- 步骤6：运行命令：

  ```sh
  docker build -t javaweb:1.0 .
  ```


### 3.3.2.基于java8构建Java项目

```dockerfile
FROM java:8-alpine
COPY ./app.jar /tmp/app.jar
EXPOSE 8090
ENTRYPOINT java -jar /tmp/app.jar
```



# 4.Docker-Compose

Docker Compose可以基于Compose文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器！

## 4.1.初识DockerCompose

Compose文件是一个文本文件，通过指令定义集群中的每个容器如何运行。格式如下：

```json
version: "3.8"
 services:
  mysql:
    image: mysql:5.7.25
    environment:
     MYSQL_ROOT_PASSWORD: 123 
    volumes:
     - "/tmp/mysql/data:/var/lib/mysql"
     - "/tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf"
  web:
    build: .
    ports:
     - "8090:8090"
```

上面的Compose文件就描述一个项目，其中包含两个容器：

- mysql：一个基于`mysql:5.7.25`镜像构建的容器，并且挂载了两个目录
- web：一个基于`docker build`临时构建的镜像容器，映射端口时8090

DockerCompose的详细语法参考官网：https://docs.docker.com/compose/compose-file/

## 4.2.使用DockerCompose部署微服务集群

### 4.2.1.compose文件

```yaml
version: "3.2"

services:
  nacos:
    image: nacos/nacos-server
    environment:
      MODE: standalone
    ports:
      - "8848:8848"
  mysql:
    image: mysql:5.7.25
    environment:
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "$PWD/mysql/data:/var/lib/mysql"
      - "$PWD/mysql/conf:/etc/mysql/conf.d/"
  userservice:
    build: ./user-service
  orderservice:
    build: ./order-service
  gateway:
    build: ./gateway
    ports:
      - "10010:10010"
```

可以看到，其中包含5个service服务：

- `nacos`：作为注册中心和配置中心
  - `image: nacos/nacos-server`： 基于nacos/nacos-server镜像构建
  - `environment`：环境变量
    - `MODE: standalone`：单点模式启动
  - `ports`：端口映射，这里暴露了8848端口
- `mysql`：数据库
  - `image: mysql:5.7.25`：镜像版本是mysql:5.7.25
  - `environment`：环境变量
    - `MYSQL_ROOT_PASSWORD: 123`：设置数据库root账户的密码为123
  - `volumes`：数据卷挂载，这里挂载了mysql的data、conf目录，其中有我提前准备好的数据
- `userservice`、`orderservice`、`gateway`：都是基于Dockerfile临时构建的

  - 内容如下：

    ```dockerfile
    FROM java:8-alpine
    COPY ./app.jar /tmp/app.jar
    ENTRYPOINT java -jar /tmp/app.jar
    ```


### 4.2.2.修改微服务配置

因为微服务将来要部署为docker容器，而容器之间互联不是通过IP地址，而是通过容器名。这里我们将order-service、user-service、gateway服务的mysql、nacos地址都修改为基于容器名的访问。

如下所示：

```yaml
spring:
  datasource:
    url: jdbc:mysql://mysql:3306/cloud_order?useSSL=false
    username: root
    password: 123
    driver-class-name: com.mysql.jdbc.Driver
  application:
    name: orderservice
  cloud:
    nacos:
      server-addr: nacos:8848 # nacos服务地址
```

### 4.2.3.打包

接下来需要将我们的每个微服务都打包。因为之前查看到Dockerfile中的jar包名称都是app.jar，因此我们的每个微服务都需要用这个名称。

可以通过修改pom.xml中的打包名称来实现，每个微服务都需要修改：

```xml
<build>
  <!-- 服务打包的最终名称 -->
  <finalName>app</finalName>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

### 4.2.4.拷贝jar包到部署目录

编译打包好的app.jar文件，需要放到Dockerfile的同级目录中。注意：每个微服务的app.jar放到与服务名称对应的目录，别搞错了。

### 4.2.5.部署

- 最后，我们需要将文件整个cloud-demo文件夹上传到虚拟机中，利用DockerCompose部署。

- 部署：进入cloud-demo目录，然后运行下面的命令：

```sh
docker-compose up -d
```



# 5.Docker镜像仓库 

## 5.1.搭建私有镜像仓库

## 5.2.推送、拉取镜像

推送镜像到私有镜像服务必须先tag，步骤如下：

- 重新tag本地镜像，名称前缀为私有仓库的地址：192.168.150.101:8080/

 ```sh
docker tag nginx:latest 192.168.150.101:8080/nginx:1.0 
 ```

- 推送镜像

```sh
docker push 192.168.150.101:8080/nginx:1.0 
```

- 拉取镜像

```sh
docker pull 192.168.150.101:8080/nginx:1.0 
```

