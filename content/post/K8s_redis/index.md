---
title: 'k8s部署redis'
description: 'k8s部署redis'
summary: "k8s部署redis"
date: '2023-10-17'
author: '飞天'
usePageBundles: true
toc: true


---

[TOC]

在K8s集群中部署Redis集群有很多方式，对于我的项目来说，一是要支持持久化；二是要支持redis容器毁灭时，能够重建就行。

Ceph有点重了。。。故采用Longhorn实现持久化存储。



## 安装Longhorn



### 先决条件

K8s集群nodes中每个node均执行以下命令：

```shell
$ sudo apt-get install open-iscsi

$ sudo apt-get install util-linux
```

### 安装

登陆rancher WEB，点击进入目标k8s集群，依次点击：Apps/Chart，输入longhorn，点击安装，等待安装完成。

安装完毕之后，集群管理页面会多出一个**Longhorn**菜单，通过此菜单可以进入**Longhorn**管理页。



## 安装Redis

**以下操作在rancher web shell UI执行：**

### 生成namespace

```shell
$ kubectl create namespace redis-server
```

### 生成文件

`pvc.yaml` - 用于生成一个持久卷用于redis容器

```shell
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: redis-pvc
  namespace: redis-server
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
```



`deployment.yaml` - 用于部署一个redis容器

```shell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-server
  namespace: redis-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-server
  template:
    metadata:
      labels:
        app: redis-server
        name: redis-server
    spec:
      containers:
      - name: redis-server
        image: redis
        args: ["--appendonly", "yes"]
        ports:
          - name: redis-server
            containerPort: 6379
        volumeMounts:
          - name: lv-storage
            mountPath: /data
        env:
          - name: ALLOW_EMPTY_PASSWORD
            value: "yes"
      imagePullSecrets:
        - name: nexus-registry
      volumes:
        - name: lv-storage
          persistentVolumeClaim:
            claimName: redis-pvc
```

说明：imagePullSecrets 是访问私有镜像仓库的配置信息，如果您的环境不涉及，可以移除此属性。

`service.yaml` - 用于暴露Redis服务

```shell
apiVersion: v1
kind: Service
metadata:
  name: redis-server
  namespace: redis-server
spec:
  selector:
    app: redis-server
  type: NodePort
  ports:
    - name: redis-port
      protocol: TCP
      port: 6379
      targetPort: 6379
      nodePort: 30001
```



### 部署

```shell
$ kubectl apply -f pvc.yaml
$ kubectl apply -f deployment.yaml
$ kubectl apply -f service.yaml
```



### 卸载

```shell
$ kubectl delete -f service.yaml  
$ kubectl delete -f deployment.yaml   
$ kubectl delete -f pvc.yaml 
```

