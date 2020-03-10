---
title: Linux 远程桌面
date: 2020-03-10 19:47:01
tags: 
---
项目需要在 docker 环境内运行视图程序，实现在外部观测程序运行状态。
单纯的 Linux 远程桌面类似操作即可。
使用的方案是远端安装 vncserver 生成虚拟桌面， 客户端使用 xtightvncviewer 监测结果。

## Docker 服务端操作
* 启动 docker 时，添加 vnc 端口映射。 vnc 服务默认端口为5901。
```
$ sudo docker run -it -p 5901:5901
```

* 安装 xfce 桌面环境, vnc 服务器
```
$ apt install xfce4 xfce4-goodies tightvncserver
```

* 启动 vncserver, 设置密码，生成初始化配置文件
```
$ vncserver
```

* 关闭 vnc 服务，修改配置文件
```
$ vncserver -kill :1
```

* 用以下配置覆写 ~/.vnc/xstartup 文件
```
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

* 重启 vnc 服务。成功启动后，会打印虚拟显示器地址： new X desktop is 9asfdsfasf8:1， 将这个地址设置到环境变量里。
```
$ vncserver
$ export DISPLAY=9asfdsfasf8:1
```

## 客户端操作

* 安装 xtightvncviewer 访问 IP:port， 输入启动vncserver时设置的密码即可
```
$ apt install xtightvncviewer
$ xtightvncviewer 127.0.0.1:5901
```
---

参考 [文章](https://cloud.tencent.com/developer/artile/1350304)