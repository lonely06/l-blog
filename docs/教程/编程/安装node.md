[官网](https://nodejs.org/en/download/)

1. 进入/usr/local目录下，创建文件夹node并进入

   ```shell
   cd /usr/local
   mkdir node && cd node
   ```

2. 使用wget命令下载安装包

   ```shell
   wget https://nodejs.org/download/release/v16.18.1/node-v16.18.1-linux-x64.tar.gz
   ```

3. 进行解压缩

   ```shell
   tar -zxvf node-v16.18.1-linux-x64.tar.gz
   ```

4. 配置环境变量

   ```shell
   # 编辑配置文件
   vim /etc/profile
   
   # 在文件末尾添加
   export NODE_HOME=/usr/local/node/node-v16.18.1-linux-x64
   export PATH=${NODE_HOME}/bin:$PATH
   
   # 刷新配置
   source /etc/profile
   ```

5. 验证安装

   ```shell
   node -v
   npm -v
   ```

   

