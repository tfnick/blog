---
title: 'rancher1.6.30+jenkins+nexus+gitea'
description: 'rancher1.6.30+jenkins+nexus+gitea'
summary: 'rancher1.6.30+jenkins+nexus+gitea'
date: '2023-09-28'
author: '飞天'
usePageBundles: true
toc: true

---

## rancher1.6



```
sudo docker run -d --restart=unless-stopped --name rancher -p 8080:8080 rancher/server:v1.6.30 --db-host 192.168.10.211 --db-user root --db-pass 123456 --db-name rancher
```



`备注`： rancher1.6.30只支持mysql5.7版本，不支持mysql8.0，甚至不能支持mysql5.7的开源版本: MariaDB 10.3



```
# 非mysql5.7时，报错可以使用如下命令查看
sudo docker logs <container_id>
```



## jenkins



```
sudo apt-get update
sudo apt install openjdk-11-jdk
wget https://github.com/jenkinsci/jenkins/releases/download/jenkins-2.450/jenkins.war
# 启动
java -Djava.awt.headless=true -jar /home/mac/jenkins/jenkins.war --webroot=%C/jenkins/war --httpPort=3533
```



将java命令封装到run.sh

```
#!/bin/bash
cd /home/mac
java -Djava.awt.headless=true -jar /home/mac/jenkins/jenkins.war --webroot=%C/jenkins/war --httpPort=3533
```



将run.sh脚本加入到自动启动

```
$ sudo vim /etc/systemd/system/jenkins.service
[Unit]
Description=Jenkins Server
After=network.target

[Service]
Type=simple
User=mac
ExecStart=/home/mac/jenkins/run.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
$ sudo systemctl daemon-reload

$ sudo systemctl enable jenkins

$ sudo systemctl start jenkins

```



`注意`jenkins升级可能会导致一些plugins执行报错，常见的是ClassNotFound，这个一般可以通过下载缺失的jar，放到%C/jenkins/war/WEB-INF/lib下即可



## nexus





## gitea