---
title : "Kind-本地快速搭建k8s集群"
description : "Kind-本地快速搭建k8s集群"
date : "2021-04-17"
aliases : ["折腾"]
author : "飞天"
toc : true
---



## 概念与架构

Kind是基于docker实现的k8s集群快速部署，能够方便快速本地搭建一个体验k8s的环境。一个类似的工具是k3d，这里只记录一下Kind的体验过程。

因为模拟实现K8s集群环境方式的落后，以下工具已经不建议去安装体验：包括microK8s,miniKube等。



![img](https://flynx.dev/images/v2-94a297f8e7b935d0500cea0c53d6c837_720w_9456925652769435314.jpg)



## 步骤

### 安装kubectl

- 本人操作系统 manjaro

```fallback
sudo pacman -S kubectl
```

- 如果上述命令提示404-找不到相关的文件，可以执行以下元数据更新命令

```fallback
sudo pacman -Syu
```

### 安装kind

```fallback
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/lobal/bin/kind
```

### 创建一个k8s集群(默认名kind)

```fallback
kind create cluster
```

### 查看kind中集群列表

```fallback
kind get clusters
```

### 删除集群

```fallback
kind delete cluster --name kind
```

### 切换集群到kind-kind

```fallback
kubectl cluster-info --context kind-kind
```

### 加载镜像到集群kind

```fallback
kind load docker-image my-custom-image-0 --name kind
```

### 手工构建镜像并更新到kind

```fallback
docker build -t my-custom-image:unique-tag ./my-image-dir  #build
kind load docker-image my-custom-image:unique-tag #load
kubectl apply -f my-manifest-using-my-image:unique-tag #deploy to kind cluster(update or create)
```

### 其他集群操作

查询集群信息

```fallback
kubectl -v 9 get pods
```

## 总结

总体来看，Kind的安装与使用还是非常棒的，可以让不熟悉K8s的朋友，快速体验K8s的功能