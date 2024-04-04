---
title: 'Pop!_os22.04上安装微信与企业微信以及其他软件'
description: 'Pop!_os22.04上安装微信与企业微信以及其他软件'
summary: "Pop!_os22.04上安装微信与企业微信以及其他软件"
date: '2024-02-25'
author: '飞天'
usePageBundles: true


---



打开优麒麟应用商店：https://www.ubuntukylin.com/applications/ 找到对应的目标应用并下载：

### 下载

1，Wine

wget http://archive.ubuntukylin.com/software/pool/partner/ukylin-wine_70.6.3.25_amd64.deb

2，企业微信

wget http://archive.ubuntukylin.com/software/pool/partner/ukylin-wxwork_1.0_amd64.deb

3，微信

wget http://archive.ubuntukylin.com/software/pool/partner/weixin_2.1.1_amd64.deb

### 安装

**sudo dpkg -i  ukylin-wine_70.6.3.25_amd64.deb**

**sudo dpkg -i weixin_2.1.1_amd64.deb**

**sudo dpkg -i ukylin-wxwork_1.0_amd64.deb**



### 卸载

```
dpkg --list|grep uky
sudo apt-get --purge remove ukylin-wxwork 
sudo apt-get --purge remove ukylin-wine:amd64
```



###  其他FAQ

1，无法显示应用启动图标

注销pop!_os并重新登陆

2，如何监控cpu 温度

```
sudo apt install psensor
```

3，win10与pop!_os 双系统时间同步

```
# 更新ubuntu的系统时间
sudo apt-get update
sudo apt-get install ntpdate
sudo ntpdate time.windows.com
# 将时间更新到硬件上
sudo hwclock --localtime --systohc
```

4、pop!_os截图软件

推荐：screenshot
