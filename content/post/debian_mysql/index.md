---
title : "debian10安装mariaDB或mysql5.7"
description : "debian10安装mariaDB或mysql5.7"
date : "2024-04-07"
aliases : ["折腾"]
author : "飞天"
toc : true
---

##  MariaDB 10.3

**MariaDB Server版本：** 10.3.31

安装：

```
# 安装mariadb
sudo apt-get install mariadb-server

service mariadb status

# 安装完毕，默认root密码为空
sudo mysql -u root

# 设置root密码为123456,按提示操作即可
sudo mysql_secure_installation

# 本地登陆
sudo mysql -u root -p 

# 授权root远程登陆
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'YOUR_ROOT_PASSWORD_HERE' WITH GRANT OPTION;

# 修改配置允许远程访问
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

修改项： bind-address            = 0.0.0.0

# 重启服务
sudo systemctl restart mariadb.service
```

卸载：

```
sudo systemctl stop mariadb.service

sudo apt-get remove --purge mariadb-server mariadb-client mariadb-common

sudo rm -rf /etc/mysql /var/lib/mysql
```





## Mysql 5.7

```
sudo apt-get update
wget -c https://repo.mysql.com//mysql-apt-config_0.8.13-1_all.deb
# 选择5.7, enable,enable,ok
sudo dpkg -i mysql-apt-config_0.8.13-1_all.deb
# 安装，界面中设置root密码
sudo apt-get update
sudo apt-get install mysql-server

# 自动启动
sudo systemctl start mysql
sudo systemctl enable mysql

sudo systemctl status mysql

# 本地登陆
sudo mysql -u root -p 

# 授权root远程登陆
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'YOUR_ROOT_PASSWORD_HERE' WITH GRANT OPTION;

# 修改配置允许远程访问
sudo vim  /etc/mysql/mysql.conf.d/mysqld.cnf
修改项： bind-address            = 0.0.0.0

# 重启服务
sudo systemctl restart mysql
```



卸载：

```
sudo systemctl stop mysql
sudo apt-get remove mysql-server
sudo apt-get autoremove
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

