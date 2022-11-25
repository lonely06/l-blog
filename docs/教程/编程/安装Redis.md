1. 
下载

   - linux：[https://download.redis.io/releases/](https://download.redis.io/releases/)
   - windows：[https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases)
2. 
安装

   1. 
linux

      1. 
上传

      2. 
解压
```shell
tar -zxvf redis-4.0.0.tar.gz -C /usr/local
```


      3. 
安装redis依赖环境：`yum install gcc-c++`

      4. 
进入/usr/local/redis-4.0.0，进行编译：`make`

      5. 
进入reids的bin目录进行安装：`make install`

```shell
/usr/local/redis-4.0.0/src/redis-server：Redis服务启动脚本
/usr/local/redis-4.0.0/src/redis-cli：Redis客户端脚本
/usr/local/redis-4.0.0/redis.conf：Redis配置文件
```

      - 其他

         - 设置redis服务后台运行，将配置文件中的`daemonize`配置项改为yes
         - 设置redis服务密码，将配置文件中的`# requirepass foobared`取消注释。foobared为默认密码
         - 设置redis允许远程连接，将配置文件中的`bind 127.0.0.1`配置项注释
   2. 
windows

      - 直接解压即可
```shell
redis-server.exe	redis服务端
redis-cli.exe		redis客户端
redis.windows.conf	redis配置文件
```

3. 
连接
```shell
# 远程连接
./redis-cli.exe -h localhost -p 6379 -a password
# 指定配置文件启动服务
./src/redis-server ./redis.conf
```


