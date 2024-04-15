---
title : "pop os上安装pycharm"
description : "pop os上安装pycharm"
date : "2024-04-15"
aliases : ["折腾"]
author : "飞天"
toc : true
---



## 下载

下载pycharm,不同版本下载地址



https://download.jetbrains.com/python/pycharm-professional-2024.1.tar.gz

https://download.jetbrains.com/python/pycharm-professional-2023.3.5.tar.gz

https://download.jetbrains.com/python/pycharm-professional-2023.3.4.tar.gz



## 设置

1，解压到 /home/mac/tools/pycharm-2023.3.4

2，创建快捷方式

```
$ gedit ~/.local/share/applications/pycharm.desktop
```

输入以下内容并保存即可。

```
[Desktop Entry]
Name=PyCharm
Exec=/home/mac/tools/pycharm-2023.3.4/bin/pycharm.sh
Icon=/home/mac/tools/pycharm-2023.3.4/bin/pycharm.png
Terminal=false
Type=Application
Categories=Application;Development;
Keywords=ide;python;charm;
StartupWMClass=jetbrains-pycharm-ce
```

