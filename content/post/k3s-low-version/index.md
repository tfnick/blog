---
title : "rancher + drone + nexus registry + gitea 自动化部署指南"
description : "rancher + drone + nexus registry + gitea 自动化部署指南"
date : "2023-09-01"
aliases : ["折腾"]
author : "飞天"
toc : true
---

[TOC]

### 部署架构

- K3s：轻量级k8s集群，没有那么注重安全，针对LOT环境特殊优化。
- RKe：另外一种轻量级k8s集群，相对K3s更注重安全，暂时没有研究。
- Rancher：一种K8s集群管理工具，当然也可以用来管理K3s、RKe集群。
- 部署结构

<img src="cicd.jpg" alt="img" style="zoom:120%;" />



| 节点    | Hostname  | 操作系统 | 安装软件                                                     | IP             | 域名           |
| ------- | --------- | -------- | ------------------------------------------------------------ | -------------- | -------------- |
| CI/CD   | cicd-node | Debian10 | **Docker19.03** + Docker-Compose1.25.4<br />Gitea<br />Drone<br />Nexus + Nginx | 192.168.10.253 | nexus.hkyx.com |
| Rancher | rancher   | Debian10 | **Docker19.03**<br />Rancher 2.7.6                           | 192.168.10.203 |                |
| Master  | server01  | Debian10 | 无                                                           | 192.168.10.204 |                |
| Worker  | worker01  | Debian10 | 无                                                           | 192.168.10.205 |                |
| Worker  | worker02  | Debian10 | 无                                                           | 192.168.10.206 |                |



### 搭建环境

#### 前置准备

- 每一个VM节点，请务必保证`hostname`、`IP`是固定且唯一的，不能重复或者冲突；同时设置域名neuxs.hkyx.com的解析地址。

  ```shell
  # 设置hostname
  $ sudo hostnamectl set-hostname xxxx
  
  $ sudo vi /etc/hosts
  127.0.1.1       xxxx
  
  # 设置静态IP
  $ sudo vi /etc/network/interfaces
  iface ens18 inet static
  address 192.168.10.xxx
  
  # 设置域名解析，保证每个VM节点都能识别该域名
  $ sudo vi /etc/hosts
  nexus.hkyx.com	192.168.10.253
  ```

  

- 请保证可以访问外网（师夷长技以制夷）

- 每个VM节点的用户都需要加入sudo组，可以执行sudo命令

- 每一个VM节点都需要关闭swap分区

  ```shell
  sudo swapoff -a
  sudo sed -i '/swap/s/^\(.*\)$/#\1/g' /etc/fstab
  ```

#### 证书

因为nexus作为镜像仓库要用到`nexus.hkyx.com`的域名 ，且rancher访问域名时必须使用`https`协议，所以我们需要生产https证书，并且同步ca证书到每一个VM节点。

##### 生产证书

登陆cicd-node，生成sans自签名证书：

```shell
# 创建ssl目录
$ cd ~ && mkdir ssl-sans && cd ssl-sans

# 生成根证书密钥ca.key
$ openssl genrsa -out ca.key 4096

# 生成自签名根证书ca.crt
$ openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=nexus.hkyx.com" \
 -key ca.key \
 -out ca.crt
 
# 生成nexus.hkyx.com.key
$ openssl genrsa -out nexus.hkyx.com.key 4096
 
# 生成nexus.hkyx.com.csr
$ openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=nexus.hkyx.com" \
    -key nexus.hkyx.com.key \
    -out nexus.hkyx.com.csr

# 生成v3.ext,sans扩展
$ cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=nexus.hkyx.com
DNS.2=nexus.hkyx
DNS.3=hostname
EOF

# 生成nexus.hkyx.com.crt
$ openssl x509 -req -sha512 -days 3650 \
  -extfile v3.ext \
  -CA ca.crt -CAkey ca.key -CAcreateserial \
  -in nexus.hkyx.com.csr \
  -out nexus.hkyx.com.crt
```

##### 同步证书

登陆cicd-node节点，复制nexus.hkyx.com.crt证书到每一个节点：

```shell
# 复制到cicd-node节点自身
$ sudo cp ~/ssl-sans/*.crt /tmp

# 复制到远程节点: rancher server01 worker01 worker02
$ sudo scp ~/ssl-sans/*.crt mac@192.168.10.203:/tmp
$ sudo scp ~/ssl-sans/*.crt mac@192.168.10.204:/tmp
$ sudo scp ~/ssl-sans/*.crt mac@192.168.10.205:/tmp
$ sudo scp ~/ssl-sans/*.crt mac@192.168.10.206:/tmp
```

##### 安装证书

在所有节点（cicd-node,rancher,server01,worker01,worker02）中分别执行如下命令：

```shell
$ apt-get install ca-certificates
# 移动证书到/usr/share/ca-certificates/local
$ sudo mkdir -p /usr/share/ca-certificates/local 
$ sudo mv /tmp/*.crt /usr/share/ca-certificates/local
# 安装证书，敲空格选中nexus.hkyx.com.crt
$ sudo dpkg-reconfigure ca-certificates
```

#### 设置cicd-node节点

#### 设置rancher节点

#### 设置server01节点

#### 设置worker01节点

#### 设置worker02节点



### CICD案例

#### 部署demo-app
