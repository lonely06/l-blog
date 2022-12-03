---
title: nginx
---
# 1. 简介

[官网](https://nginx.org/)

Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好。


# 2. 命令

```shell
# 查看版本
nginx -v
nginx -V

# 检查配置文件
nginx -t

# 启动
nginx

# 停止
nginx -s stop

# 重新加载
nginx -s reload
```


# 3. 应用


## 3.1 配置文件架构

- 
配置文件修改后，需重新加载才可生效
```shell
nginx -s reload
```


| 区域 | 职责 |
| --- | --- |
| 全局块 | 配置和nginx运行相关的全局配置 |
| events块 | 配置和网络连接相关的配置 |
| http块 | 配置代理、缓存、日志记录、虚拟主机等配置 |


![](http://img.lonely.icu/lonely-md/202210212133916.png)

> 在全局块、events块以及http块中，我们经常配置的是http块。

> 在http块中可以包含多个server块,每个server块可以配置多个location块。



## 3.2 部署静态资源

- 
将文件复制到Nginx安装目录下的html目录中

- 
在nginx.conf中进行配置
```
server {
    listen 80;				#监听端口	
    server_name localhost;	#服务器名称
    location / {			#匹配客户端请求url
        root html;			#指定静态资源根目录
        index index.html;	#指定默认首页
    }
}
```




## 3.3 方向代理

- 对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源，反向代理服务器负责将请求转发给目标服务器。用户不需要知道目标服务器的地址，也无须在用户端作任何设定，对于用户来说，访问反向代理服务器是完全无感知的。

  ![](http://img.lonely.icu/lonely-md/202210212137864.png)

- 
在nginx.conf中进行配置
```
server {
    listen 81;
    server_name localhost;
    location / {
        proxy_pass http://192.168.200.201:8080; 	#反向代理配置，将请求转发到指定服务
    }
}
# 当访问nginx的81端口时，根据反向代理配置，会将请求转发到http://192.168.200.201:8080对应的服务上
```




## 3.4 负载均衡

- 应用集群：将同一应用部署到多台机器上，组成应用集群，接收负载均衡器分发的请求，进行业务处理并返回响应数据
- 负载均衡器：将用户请求根据对应的负载均衡算法分发到应用集群中的一台服务器进行处理

![](http://img.lonely.icu/lonely-md/202210212146399.png)

- 
Nginx的负载均衡是基于反向代理的，只不过此时所代理的服务器不是一台，而是多台。

- 
在nginx.conf中进行配置
```
#upstream指令可以定义一组服务器
upstream targetserver{	
    server 192.168.200.201:8080;
    server 192.168.200.201:8081;
}

server {
    listen       8080;
    server_name  localhost;
    location / {
        proxy_pass http://targetserver;
    }
}
```


- 
负载均衡策略
| **名称** | **说明** | 特点 |
| --- | --- | --- |
| 轮询 | 默认方式 |  |
| weight | 权重方式 | 根据权重分发请求,权重大的分配到请求的概率大 |
| ip_hash | 依据ip分配方式 | 根据客户端请求的IP地址计算hash值， 根据hash值来分发请求, 同一个IP发起的请求, 会发转发到同一个服务器上 |
| least_conn | 依据最少连接方式 | 哪个服务器当前处理的连接少, 请求优先转发到这台服务器 |
| url_hash | 依据url分配方式 | 根据客户端请求url的hash值，来分发请求, 同一个url请求, 会发转发到同一个服务器上 |
| fair | 依据响应时间方式 | 优先把请求分发给处理请求时间短的服务器 |



   - 
权重的配置
```
# upstream指令可以定义一组服务器
upstream targetserver{	
    server 192.168.200.201:8080 weight=10;
    server 192.168.200.201:8081 weight=5;
}
# weight权重是相对的，在上述的配置中，效果就是，在大数据量的请求下，最终8080接收的请求数是8081的两倍。
```

# 其他

## Nginx中alias与root的区别:

- Nginx指定文件路径有两种方式root和alias，这两者的用法区别在于对URI的处理方法不同。

- alias:

  ```
  location /abc/{
  	alias /usr/local/nginx/html/admin/；
  }
  ```

  - 若按照上述配置的话，则访问/abc/目录里面的文件时，ningx会自动去/usr/local/nginx/html/admin目录找文件。

- root:

  ```
  location /abc/ {
  	root /usr/local/nginx/html/admin；
  }
  ```

  - 若按照这种配置的话，则访问/abc/目录下的文件时，nginx会去/usr/local/nginx/html/admin/abc下找文件。

- alias是一个目录别名的定义，root则是最上层目录的定义。

- 还有一个重要的区别是alias后面必须要用“/”结束，否则会找不到文件的。而root则可有可无。
