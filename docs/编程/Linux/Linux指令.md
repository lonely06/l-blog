---
title: Linux指令
sidebar_position: 1
---

# 1. VMware


## 1.1 修改网卡配置项

```shell
cd /etc/sysconfig/network-scripts
vim ifcfg-ens33
# 将文件中最后一行的ONBOOT=no改为yes
```

![](http://img.lonely.icu/lonely-md/202210151719584.png)

- 疑难问题：[service network restart失败最全解决方案](https://blog.csdn.net/qq_44833733/article/details/127953041)


## 1.2 设置静态ip

1.  设置静态ip 
```shell
BOOTPROTO=static
IPADDR="192.168.138.100"        # 设置的静态IP地址
NETMASK="255.255.255.0"         # 子网掩码
GATEWAY="192.168.138.2"         # 网关地址
DNS1="192.168.138.2"            # DNS服务器
```
<br />VMware->编辑->虚拟网络编辑器

![](http://img.lonely.icu/lonely-md/202210151927858.png)<br />须保持子网掩码、网关地址、DNS服务器中的与图片中的保持一致 

2.  重启网络服务 `systemctl restart network` 
3.  ![](http://img.lonely.icu/lonely-md/202210151931064.png)<br />更改选框中的内容与虚拟中的设置保持一致![](http://img.lonely.icu/lonely-md/202210151931759.png) 


# 2. 目录结构

![](http://img.lonely.icu/lonely-md/202210151720644.png)

| 编号 | 目录 | 含义 |
| --- | --- | --- |
| 1 | /bin | 存放二进制可执行文件 |
| 2 | /boot | 存放系统引导时使用的各种文件 |
| 3 | /dev | 存放设备文件 |
| 4 | /etc | 存放系统配置文件 |
| 5 | /home | 存放系统用户的文件 |
| 6 | /lib | 存放程序运行所需的共享库和内核模块 |
| 7 | /opt | 额外安装的可选应用程序包所放置的位置 |
| 8 | /root | 超级用户目录 |
| 9 | /sbin | 存放二进制可执行文件，只有root用户才能访问 |
| 10 | /tmp | 存放临时文件 |
| 11 | /usr | 存放系统应用程序 |
| 12 | /var | 存放运行时需要改变数据的文件，例如日志文件 |



# 3. 常用命令
| 命令 | 描述 |
| --- | --- |
| pwd | 查看当前所在目录 |
| clear | 清屏 |
| ip addr | 查看系统ip |



## 3.1 文件目录操作命令


### 3.1.1 ls 显示指定目录下内容

- `ll`
- `ls [al] [目录]` 
   - -a 显示所有文件（隐藏文件也会显示）
   - -l 显示文件的详细信息


### 3.1.2 cd 进入指定目录

- `cd [目录]` 
   - ~  用户的home目录
   - .  当前目录
   - .. 当前目录的上级目录


### 3.1.3 cat 显示文件内容

- `cat [-n] 文件名` 
   - -n 由1开始对所有输出的行数编号


### 3.1.4 more 以分页形式显示文件内容

- `more 文件名`
- 操作说明： 
   - 回车键   向下滚动一行
   - 空格键   向下滚动一屏
   - b       返回上一屏
   - q或crtl+C  退出


### 3.1.5 tail 查看文件末尾内容

- `tail [-f] 文件名` 
   - -f 动态读取文件末尾内容

```shell
tail -20 /etc/profile	#显示/etc目录下的profile文件末尾20行的内容
```


### 3.1.6 mkdir 创建目录

- `mkdir [-p] 文录名` 
   - -p 确保目录名称存在，不存在的就创建一个。通过此选项，可以实现多层目录同时创建

```shell
mkdir itcast  #在当前目录下，建立一个名为itcast的子目录
mkdir -p itcast/test   #在工作目录下的itcast目录中建立一个名为test的子目录，若itcast目录不存在，则建立一个
```


### 3.1.7 rmdir 删除空目录

- `rmdir [-p] 目录名` 
   - -p 当子目录被删除后使父目录为空目录的话，则一并删除

```shell
rmdir itcast   #删除名为itcast的空目录
rmdir -p itcast/test   #删除itcast目录中名为test的子目录，若test目录删除后itcast目录变为空目录，则也被删除
rmdir itcast*   #删除名称以itcast开始的空目录
```


### 3.1.8 rm 删除文件或目录

- `rm [-rf] name` 
   - -r 将目录及目录中所有文件（目录）逐一删除，即递归删除
   - -f 无需确认，直接删除


### 3.1.9 cp 复制文件或目录

- `cp [-r] source dest` 
   - -r 复制该目录下所有的子目录和文件

```shell
cp hello.txt itcast/            #将hello.txt复制到itcast目录中
cp hello.txt ./hi.txt           #将hello.txt复制到当前目录，并改名为hi.txt
cp -r itcast/ ./itheima/    	#将itcast目录和目录下所有文件复制到itheima目录下
cp -r itcast/* ./itheima/ 	 	#将itcast目录下所有文件复制到itheima目录下
```


### 3.1.10 mv 移动/改名文件或目录

- `mv source dest`

```shell
mv hello.txt hi.txt                 #将hello.txt改名为hi.txt
mv hi.txt itheima/                  #将文件hi.txt移动到itheima目录中
mv hi.txt itheima/hello.txt   		#将hi.txt移动到itheima目录中，并改名为hello.txt
mv itcast/ itheima/                 #如果itheima目录不存在，将itcast目录改名为itheima
mv itcast/ itheima/                 #如果itheima目录存在，将itcast目录移动到itheima目录中
```


## 3.2 打包压缩

- `tar [-zcxvf] fileName [files]` 
   - 包文件后缀为.tar表示只是完成了打包，并没有压缩
   - 包文件后缀为.tar.gz表示打包的同时还进行了压缩
   - -z: z代表的是gzip，通过gzip命令处理文件，gzip可以对文件压缩或者解压
   - -c: c代表的是create，即创建新的包文件
   - -x: x代表的是extract，实现从包文件中还原文件
   - -v: v代表的是verbose，显示命令的执行过程
   - -f: f代表的是file，用于指定包文件的名称

```shell
# 打包
tar -cvf hello.tar ./*		  		#将当前目录下所有文件打包，打包后的文件名为hello.tar
tar -zcvf hello.tar.gz ./*		  	#将当前目录下所有文件打包并压缩，打包后的文件名为hello.tar.gz
# 解压
tar -xvf hello.tar		  			#将hello.tar文件进行解包，并将解包后的文件放在当前目录
tar -zxvf hello.tar.gz		  		#将hello.tar.gz文件进行解压，并将解压后的文件放在当前目录
tar -zxvf hello.tar.gz -C /usr/local     #将hello.tar.gz文件进行解压，并将解压后的文件放在/usr/local目录
```


## 3.3 查找


### 3.3.1 find 在指定目录下查找文件

- `find dirName -option fileName`

```shell
find  .  –name "*.java"			#在当前目录及其子目录下查找.java结尾文件
find  /itcast  -name "*.java"	#在/itcast目录及其子目录下查找.java结尾的文件
```


### 3.3.2 grep 从指定文件中查找指定文本

- `grep word fileName`

```shell
grep Hello HelloWorld.java	#查找HelloWorld.java文件中出现的Hello字符串的位置
grep hello *.java			#查找当前目录中所有.java结尾的文件中包含hello字符串的位置
```


## 3.4 防火墙
| 操作 | 指令 | 备注 |
| --- | --- | --- |
| 查看防火墙状态 | systemctl status firewalld / firewall-cmd --state |  |
| 暂时关闭防火墙 | systemctl stop firewalld |  |
| 永久关闭防火墙(禁用开机自启) | systemctl disable firewalld | 下次启动,才生效 |
| 暂时开启防火墙 | systemctl start firewalld |  |
| 永久开启防火墙(启用开机自启) | systemctl enable firewalld | 下次启动,才生效 |
| 开放指定端口 | firewall-cmd --zone=public --add-port=8080/tcp --permanent | 需要重新加载生效 |
| 关闭指定端口 | firewall-cmd --zone=public --remove-port=8080/tcp --permanent | 需要重新加载生效 |
| 立即生效(重新加载) | firewall-cmd --reload |  |
| 查看开放端口 | firewall-cmd --zone=public --list-ports |  |



## 3.5 查询进程

- `ps -ef | grep fileName` 
   - ps 是linux下非常强大的进程查看命令，通过ps -ef可以查看当前运行的所有进程的详细信息
   - |  在Linux中称为管道符，可以将前一个命令的结果输出给后一个命令作为输入


## 3.6 查询/卸载系统中安装的软件

- 查询 `rpm -qa | grep 文件名`
- 卸载 `rpm -e --nodeps 软件名称`


## 3.6 关闭进程

- `kill -9 进程id` 
   - -9 表示强制结束

## 3.7 权限

- 描述
   1. `chmod`（英文全拼：change mode）命令是控制用户对文件的权限的命令
   2. Linux中的权限分为三种 ：读(r)、写(w)、执行(x)
   3. Linux文件权限分为三级 : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）
   4. 只有文件的所有者和超级用户可以修改文件或目录的权限
   5. 要执行Shell脚本需要有对此脚本文件的执行权限(x)，如果没有则不能执行
- ![](http://img.lonely.icu/lonely-md/202210160925506.png)
| 值 | 权限 | rwx |
| --- | --- | --- |
| 7 | 读 + 写 + 执行 | rwx |
| 6 | 读 + 写 | rw- |
| 5 | 读 + 执行 | r-x |
| 4 | 只读 | r-- |
| 3 | 写 + 执行 | -wx |
| 2 | 只写 | -w- |
| 1 | 只执行 | --x |
| 0 | 无 | --- |

- 例
```shell
chmod 777 bootStart.sh   为所有用户授予读、写、执行权限
chmod 755 bootStart.sh   为文件拥有者授予读、写、执行权限，同组用户和其他用户授予读、执行权限
chmod 210 bootStart.sh   为文件拥有者授予写权限，同组用户授予执行权限，其他用户没有任何权限
```

# 4. 程序安装方式

在Linux中有4种安装方式

| 安装方式 | 特点 |
| --- | --- |
| 二进制发布包安装 | 软件已经针对具体平台编译打包发布，只要解压，修改配置即可 |
| rpm安装 | 软件已经按照redhat的包管理规范进行打包，使用rpm命令进行安装，不能自行解决库依赖问题 |
| yum安装 | 一种在线软件安装方式，本质上还是rpm安装，自动下载安装包并安装，安装过程中自动解决库依赖问题(安装过程需要联网) |
| 源码编译安装 | 软件以源码工程的形式发布，需要自己编译打包 |



# 5. 查看/替换系统源

-  查看当前系统源 
   - `yum repolist`
-  更换阿里源 
   1.  安装wget，`yum install wget` 
   2.  备份当前源 
```shell
cd /etc/yum.repos.d/	# 切换目录
mkdir bak				# 创建目录
mv *.repo bak/			# 移动现有源
```


   3.  下载阿里源 
```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```


   4.  重新生成cache 
```shell
yum clean all
yum makecache
```


   5.  再次查看源 

# 6. 文件上传工具-lrzsz 

1.  安装lrzsz 
```shell
# 搜索lrzsz安装包
yum list lrzsz
# 安装lrzsz
yum install lrzsz
```


2.  使用
   - 在命令行中输入`rz` 
