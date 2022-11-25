1. 
[下载](http://nginx.org/en/download.html)

2. 
安装

   1. 
安装依赖。需要c语言编译环境，及正则表达式库等第三方依赖库。
```shell
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
```


   2. 
下载安装包
```shell
yum install wget
wget https://nginx.org/download/nginx-1.16.1.tar.gz
```


   3. 
解压安装包
```shell
tar -zxvf nginx-1.16.1.tar.gz
```


   4. 
配置nginx编译环境
```shell
cd nginx-1.16.1
./configure --prefix=/usr/local/nginx # --prefix 指定的目录，就是我们安装Nginx的目录
```


   5. 
编译安装
```shell
make & make install
```


   6. 
配置到全局

      1. 
`vim /etc/profile`
<br />可能需先配置jdk
<br />![](https://cdn.jsdelivr.net/gh/lonely06/images@main/healthy/202210212121079.png#alt=image-20221021212133981)

      2. 
`source /etc/profile`重新加载文件。即可在任意目录下执行nginx命令

3. 
目录结构

   - 重点目录/文件
| 目录/文件 | 说明 | 备注 |
| --- | --- | --- |
| conf | 配置文件的存放目录 |  |
| conf/nginx.conf | Nginx的核心配置文件 | conf下有很多nginx的配置文件，我们主要操作这个核心配置文件 |
| html | 存放静态资源(html, css, ) | 部署到Nginx的静态资源都可以放在html目录中 |
| logs | 存放nginx日志(访问日志、错误日志等) |  |
| sbin/nginx | 二进制文件，用于启动、停止Nginx服务 |  |

