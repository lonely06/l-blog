
## 安装app步骤
1. 随便启动一个app程序或启动Android子系统
2. 在cmd中输入`adb connect 127.0.0.1:58526`

出现successfully即为连接成功

3. 在cmd中输入`adb install `(注意空格)

然后将需要安装得app拖入cmd窗口，回车，等待安装成功即可。

## 注意

### 报错误：adb: error: failed to get feature set: more than one device/emulator

1. 检查是否是多个设备，输入命令`adb devices`
2. 将安装命令换为`adb -s 127.0.0.1:58526 install `

## 原视频教程
[点击查看【bilibili】](https://player.bilibili.com/player.html?bvid=BV1yb4y1s7Xg)
