+++
title = "微服manjaro go环境搭建务分层模型"
description = "manjaro go环境搭建"
date = "2021-03-23"
aliases = ["折腾"]
author = "飞天"
toc = true
+++



## 机器环境

```

日期： 2021-03-23
操作系统： Manjaro
go版本： go1.16.2 linux/amd64
GoLand： 商业IDE 
Golite:  开源IDE 
```

## 安装过程

### 安装go

``` shell
sudo pacman -S go
```

### 配置go

```shell
vim ~/.profile
```

添加

```shell
# set go environment
export GOROOT=/usr/lib/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
# set proxy
export GOPROXY=https://goproxy.io,direct
export GOSUMDB=off
export GO111MODULE=on
```

使生效

```shell
source ~/.profile
```

检查

```shell
go env
```

### 配置Goland(可选，推荐)

`File` -> `Setting` -> `Tools` -> `File Watchers`,添加以下三个工具项：

- **go fmt**,格式化
- **goimports**，自动引入
- **golangci-lint**，Go静态代码检查工具


### 安装delve(可选)


### GoLand配置delve(可选)

