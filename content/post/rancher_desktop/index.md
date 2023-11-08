---
title : "macos上rancher desktop安装与配置"
description : "macos上rancher desktop安装与配置"
date : "2023-11-08"
aliases : ["折腾"]
author : "飞天"
toc : true
---

[toc]

## 安装

rancher desktop优势：

- 完全免费，可以替代docker desktop，并且体验更好
- 开发环境，宿主机可以快速方便的启动或关闭k8s集群

安装rancher desktop比较简单：

- 下载： https://rancherdesktop.io/ 根据macos芯片选择合适的安装包
- 保证可以访问外网的情况下，双击安装即可，安装过程中会自己去下载需要的依赖包



## 设置

安装完rancher desktop之后，默认会在~/.rd/bin下安装了docker, helm,docker-compose等可执行文件，命令行执行: docker info可以查看信息。

```shell
$ docker info
```

如果需要设置镜像地址，或者配置私服忽略https证书验证，按如下进行配置：

```shell
# 1，进入rancher实例
$ LIMA_HOME="$HOME/Library/Application Support/rancher-desktop/lima" "/Applications/Rancher Desktop.app/Contents/Resources/resources/darwin/lima/bin/limactl" shell 0

# 2，配置实例下的/etc/docker/daemon.json
$ vi /etc/docker/daemon.json
{
 "registry-mirrors" : [
   "https://hub-mirror.c.163.com",
   "https://docker.mirrors.ustc.edu.cn"
 ],
 "insecure-registries" : [
   "nexus.hkyx.com"
 ],
 "debug" : true,
 "experimental" : true
}
# 3，退出rancher desktop，并重新启动后，再次执行docker info
$ docker info
```

Docker info显示信息如下（可以看到Insecure Registries以及Registry Mirrors）：

```
Client:
 Version:    24.0.6-rd
 Context:    rancher-desktop
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.11.2
    Path:     /Users/mac/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.22.0
    Path:     /Users/mac/.docker/cli-plugins/docker-compose

Server:
 Containers: 21
  Running: 11
  Paused: 0
  Stopped: 10
 Images: 12
 Server Version: 23.0.6
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 0cae528dd6cb557f7201036e9f43420650207b58
 runc version: 860f061b76bb4fc671f0f9e900f7d80ff93d4eb7
 init version: 
 Security Options:
  seccomp
   Profile: builtin
 Kernel Version: 6.1.57-0-virt
 Operating System: Alpine Linux v3.18
 OSType: linux
 Architecture: x86_64
 CPUs: 6
 Total Memory: 11.69GiB
 Name: lima-rancher-desktop
 ID: 7a4210df-22d4-49f8-a71c-9dd2a29cb79b
 Docker Root Dir: /var/lib/docker
 Debug Mode: true
  File Descriptors: 86
  Goroutines: 87
  System Time: 2023-11-08T01:42:22.566832341Z
  EventsListeners: 0
 Experimental: true
 Insecure Registries:
  nexus.hkyx.com
  127.0.0.0/8
 Registry Mirrors:
  https://hub-mirror.c.163.com/
  https://docker.mirrors.ustc.edu.cn/
 Live Restore Enabled: false
```

