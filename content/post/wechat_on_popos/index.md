---
title: 'Pop!_os22.04上安装微信与企业微信(完美)'
description: 'Pop!_os22.04上安装微信与企业微信(完美)'
summary: "Pop!_os22.04上安装微信与企业微信(完美)"
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

**sudo dpkg -i ukylin-wxwork_1.0_amd64.deb **



### 卸载

```
dpkg --list|grep uky
sudo apt-get --purge remove ukylin-wxwork 
sudo apt-get --purge remove ukylin-wine:amd64
```

###  FAQ

1，无法显示应用启动图标

注销并重新登陆