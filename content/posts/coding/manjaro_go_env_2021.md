---
title: 'manjaro go环境搭建'
date: 2021-03-23
tags: [折腾]
toc: true

---

## 机器环境

```

日期： 2021-03-23
操作系统： Manjaro
go版本： go1.16.2 linux/amd64
GoLand： 商业开发IDE 
Golite:  开源开发IDE 
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
export GO111MODULE=auto
```

使生效

```shell
source ~/.profile
```

检查

```shell
go env
```

### 安装delve(可选)


### GoLand配置delve(可选)

