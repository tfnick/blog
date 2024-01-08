---
title: 'centos7.2 + dataease安装体验'
description: 'centos7.2 + dataease安装体验'
summary: "centos7.2 + dataease安装体验"
date: '2024-01-08'
author: '飞天'
usePageBundles: true
toc: true

---



## 背景

最近想调研一下开源数据可视化工具，比如metabase、dataease，于是准备基于centos 7.2平台安装一下dataease。



## 步骤

### 安装centos7.2

**适用于 x86_64 架构：**

**[CentOS-7-x86_64-DVD-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-DVD-1511.iso)**                       09-Dec-2015 23:15      4G

[CentOS-7-x86_64-DVD-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-DVD-1511.torrent)                   14-Dec-2015 14:00    162K

[CentOS-7-x86_64-Everything-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-Everything-1511.iso)                09-Dec-2015 22:39      7G

[CentOS-7-x86_64-Everything-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-Everything-1511.torrent)            14-Dec-2015 14:00    290K

[CentOS-7-x86_64-LiveGNOME-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-LiveGNOME-1511.iso)                 10-Dec-2015 12:09      1G

[CentOS-7-x86_64-LiveGNOME-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-LiveGNOME-1511.torrent)             14-Dec-2015 14:00     46K

[CentOS-7-x86_64-LiveKDE-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-LiveKDE-1511.iso)                   10-Dec-2015 12:09      2G

[CentOS-7-x86_64-LiveKDE-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-LiveKDE-1511.torrent)               14-Dec-2015 14:00     67K

[CentOS-7-x86_64-Minimal-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso)                   09-Dec-2015 23:03    603M

[CentOS-7-x86_64-Minimal-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-Minimal-1511.torrent)               14-Dec-2015 14:00     24K

[CentOS-7-x86_64-NetInstall-1511.iso](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-NetInstall-1511.iso)                09-Dec-2015 22:34    376M

[CentOS-7-x86_64-NetInstall-1511.torrent](https://archive.kernel.org/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-NetInstall-1511.torrent)            14-Dec-2015 14:00     15K



```
版本说明：

DVD：        # 标准安装镜像，一般常用。
Minimal：    # 最小安装镜像，自带的软件最少。
Everything： # 标准的基础上集成所有软件，镜像最大。
NetInstall： # 网络安装镜像，包小，但需联网才能安装。
```



更多版本请参阅《[下载 CentOS ISO](https://renwole.com/linux-downloads/download-centos-linux-iso-images)》。

### 安装Dataease



访问https://community.fit2cloud.com/#/products/dataease/downloads，下载database当前最新社区版v2.2.0版本



#### 安装要求

按照部署服务器要求准备好部署环境后，可通过 DataEase 安装脚本快速部署。
**部署服务器要求：**

- 操作系统: CentOS/RHEL 7 及以上 64 位系统
- CPU/内存: 4核8G
- 磁盘空间: 200G
- **可访问互联网**

将上传至服务器的安装包解压好，在安装包目录里执行以下脚本进行快速安装：

```
/bin/bash install.sh
```

DataEase 服务器版是一款 B/S 架构的产品，即浏览器/服务器结构，在服务器安装完成后，客户端通过浏览器访问以下地址，即可开始使用。

```
http://服务器 IP 地址：服务运行端口（若没有修改则默认为 8100）
使用默认用户名 admin 密码 DataEase@123456 进行登录。
```

#### 执行安装

```shell
# 解压
cd /opt
tar -xvf dataease-offline-installer-v2.2.0.tar.gz
cd dataease-offline-installer-v2.2.0
# 安装
/bin/bash install.sh
```

#### 执行卸载

```shell
# 卸载
/bin/bash uninstall.sh
```

