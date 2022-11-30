### 1. 安装vsftpd

1. 安装vsftpd

   ```shell
   yum install -y vsftpd
   ```

2. 设置开机自启

   ```shell
   systemctl enable vsftpd
   ```

3. 启动ftp服务

   ```shell
   systemctl start vsftpd
   ```

4. 查看服务是否启动成功

   ```shell
   netstat -antup | grep ftp
   ```

​	显示如下，则为启动成功

​	![image-20221130162954961](http://img.lonely.icu//lonely-md/202211301630077.png)

### 2. 配置vsftpd

1. 为ftp服务创建用户，以admin为例

   ```shell
   useradd admin
   ```

2. 设置admin用户的密码（回车确认设置，密码默认不显示）

   ```shell
   password admin
   ```

3. 创建ftp服务使用的目录，以`/usr/local/ftp`为例

   ```shell
   mkdir /usr/local/ftp
   ```

4. 修改目录权限

   ```shell
   chown -R admin:admin /usr/local/ftp
   ```

5. 编辑`vsftpd.conf`文件

   ```shell
   vim /etc/vsftpd/vsftpd.conf
   ```

   1. 修改以下配置参数，设置匿名用户和本地用户的登录权限，设置指定例外用户列表文件的路径，并开启监听 IPv4 sockets。

      ```
      anonymous_enable=NO
      local_enable=YES
      chroot_local_user=YES
      chroot_list_enable=YES
      chroot_list_file=/etc/vsftpd/chroot_list
      listen=YES
      ```

   2. 注释 `listen_ipv6=YES` 配置参数，关闭监听 IPv6 sockets。

      ```
      #listen_ipv6=YES
      ```

   3. 添加以下配置参数，开启被动模式，设置本地用户登录后所在目录，以及云服务器建立数据传输可使用的端口范围值。

      ```
      local_root=/usr/local/ftp
      allow_writeable_chroot=YES
      pasv_enable=YES
      pasv_address=xxx.xx.xxx.xx #请修改为您的轻量应用服务器公网 IP
      pasv_min_port=40000
      pasv_max_port=45000
      ```

6. 创建并编辑`chroot_list`文件

   ```
   vim /etc/vsftpd/chroot_list
   ```

   按 **i** 进入编辑模式，输入用户名，一个用户名占据一行，设置完成后按 **Esc** 并输入 **:wq** 保存后退出。
   您若没有设置例外用户的需求，可跳过此步骤，输入 **:wq** 退出文件。

7. 重启ftp服务

   ```shell
   systemctl restart vsftpd
   ```

### 3. 放行端口

- 主动模式：放通端口21。
- 被动模式：放通端口21，及 **修改配置文件** 中设置的 `pasv_min_port` 到 `pasv_max_port` 之间的所有端口，本文放通端口为40000 - 45000。

### 其他

#### 设置ftp主动模式

- 主动模式需修改的配置如下，其余配置保持默认设置：

  ```
  anonymous_enable=NO      #禁止匿名用户登录
  local_enable=YES         #支持本地用户登录
  chroot_local_user=YES    #全部用户被限制在主目录
  chroot_list_enable=YES   #启用例外用户名单
  chroot_list_file=/etc/vsftpd/chroot_list  #指定用户列表文件，该列表中的用户不被锁定在主目录
  listen=YES               #监听IPv4 sockets
  #在行首添加#注释掉以下参数
  #listen_ipv6=YES         #关闭监听IPv6 sockets
  #添加下列参数
  allow_writeable_chroot=YES
  local_root=/var/ftp/test #设置本地用户登录后所在的目录
  ```

- 设置完成后，进行`配置vsftpd`中的第6步完成配置