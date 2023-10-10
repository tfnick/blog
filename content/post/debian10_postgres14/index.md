---
title : "Debian 10安装Postgres14"
description : "Debian 10安装Postgresql 14"
date : "2023-09-21"
aliases : ["折腾"]
author : "飞天"
toc : true

---



## 说明 

默认debian10安装的Postgresql版本是11，因项目需要，需要升级到Postgresql 14。



## 安装步骤

```shell
$ sudo apt update && sudo apt upgrade

$ sudo apt -y install gnupg2 wget vim

$ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

$ sudo wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

$ sudo apt -y update

$ sudo apt install postgresql-14

# 查看版本
$ sudo -u postgres psql -c "SELECT version();"

# 查看进程状态
$ systemctl status postgresql

```

### 初始化

```shell
$ sudo vi /etc/postgresql/14/main/postgresql.conf
# 修改项
listen_addresses = '*'   # what IP address(es) to listen on;

$ sudo vi /etc/postgresql/14/main/pg_hba.conf

# IPv4 local connections: 
#host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             0.0.0.0/0               scram-sha-256

$ sudo service postgresql restart


# 设置postgres默认密码为123456
$ sudo -u postgres psql

ALTER Role postgres WITH PASSWORD '123456'; #注意命令后面又分号
```

