---
title: 'docker安装minio'
description: 'docker安装minio'
summary: "docker安装minio"
date: '2023-10-16'
author: '飞天'
usePageBundles: true
toc: true

---

基于Docker快速安装minio单节点：

```shell
$ sudo docker  search  minio

$ sudo docker pull monio/monio

$ sudo mkdir -p /var/minio/data && sudo mkdir -p /var/minio/config

$ sudo docker run -p 9000:9000 -p 9090:9090 --name minio -d --restart=always -e "MINIO_ACCESS_KEY=minio" -e "MINIO_SECRET_KEY=minio123456" -v /var/minio/data:/data -v /var/minio/config:/root/.minio minio/minio server /data --console-address ":9090" --address ":9000"
```

访问：http://MINIO_IP:9090