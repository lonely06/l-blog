安装步骤

1. 进群 找玛卡巴卡获取许可 

`@NolanNarkbot`  点击start后<br />在群里使用命令菜单`/narksq@NolanNarkbot`获取许可 

2. 在群里发送`/narksq@NolanNarkbot` 获取到授权之后 改名为Nark.lic
3. `mkdir /root/Ark`
4. `cd /root/Ark && mkdir -p Config`  
5. 将授权文件放入`/root/Ark/Config`中 
6. 将Config.json 文件放入`/root/Ark/Config`
7. 建立 日志文件夹  `cd /root/Ark && mkdir -p logfile` 
8. `cd /root/Ark` 准备拉取启动容器

arm需要将latest 更换为arm 即可<br />`sudo docker run   --name nark -p 5701:80  -d  -v  "$(pwd)"/Config:/app/Config \ -v  "$(pwd)"/logfile:/app/logfile  \ -it --privileged=true  nolanhzy/nark:latest`<br />更新<br />`docker run --rm     -v /var/run/docker.sock:/var/run/docker.sock     containrrr/watchtower -c     --run-once nark`
