---
title : "无线USB键盘进不去BIOS"
description : "无线USB键盘进不去BIOS"
date : "2024-02-25"
aliases : ["折腾"]
author : "飞天"
toc : true
---



最近购买了HP elitedesk 805g8 小主机，且在独立硬盘上先后安装了pop os系统与windows10系统，最终达到的效果是：

- **pop os**作为主系统，默认开机直接进入**pop os**系统
- 启动主系统过程中，可以连续点击**空格键**进入系统选择界面，可以选择windows boot manager进入windows10
- 启动系统过程中，可以连续点击**F10**进入**BIOS**设置



不过，中间也遇到一些问题，记录解决过程如下：

1. 由于使用的是USE无线键盘，导致按F10无法进取BIOS

   先使用**有线键盘**按**F10**进入BIOS，找到**Fast Boot**选项，取消勾选；同时，保证**USB Legacy Support**选项被选中。

   **退出并保存BIOS设置**之后重启电脑，这样**USB无线键盘**按**F10**亦可以进入**BIOS**了

2. 由于使用的是USB无线键盘，导致按空格无法进入系统选择界面

   解决办法同上



顺带一提，入手了科腾900系游戏方向盘带着儿子玩了几把尘埃3，有点意思。