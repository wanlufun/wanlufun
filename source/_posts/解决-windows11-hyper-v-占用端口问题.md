---
title: 解决 windows 11 hyper-v 占用端口问题
abbrlink: fe35229
date: 2024-01-10 16:32:17
cover: https://images.wanlu.fun/picgo/202401101840582.png
tags:
  - Windows 11
---

## 问题描述

在我们日常的开发中，会经常遇到端口被占用，比如：8080、8081 等

我们一般的做法是使用： `netstat -ano | findstr "8101"` 命令，去查找是什么进程占用的端口，但是有时端口明明被占用了，但是输入这段命令确没有任何输出。

![image-20240110184013549](https://images.wanlu.fun/picgo/202401101840582.png)

这个原因是 hyper-v 在启用时，会随机为**容器宿主网络服务**占用一部分端口。



## 问题解决

在 windows 11 中存在 **TCP 动态端口** 的概念

使用命令 `netsh int ipv4 show dynamicport tcp` ，可以查看 **TCP 动态端口** 的范围

![image-20240110164915396](https://images.wanlu.fun/picgo/202401101649453.png)

<div style="text-align: center">图 1 （注：本电脑的 TCP 动态端口已更改）</div>

这段输出的意思是：从 55000 - 65535 范围内的端口都可能会被 hyper-v 随机占用。

可以使用 `netsh int ipv4 show excludedportrange protocol=tcp` 命令，查看已经被 hyper-v 占用端口

![image-20240110165825686](https://images.wanlu.fun/picgo/202401101658818.png) 

<div style="text-align: center">图 2 </div>

如果以上端口范围包含：8080 等端口，就会出现占用。

可以通过 `netsh int ipv4 set dynamic tcp start=55000 num=10536` `netsh int ipv6 set dynamic tcp start=55000 num=10536` 命令去调整 **TCP 动态端口范围** 。

![image-20240110170746454](https://images.wanlu.fun/picgo/202401101707619.png)

<div style="text-align: center">图 3 </div>

此时重启电脑，Hyper-V 就会从 55000 - 65565 范围内选取一些端口进行占用，而不会占用日常开发的端口。



## 参考文献

[1]王兆基. (2022, January 24). 解决 Windows 10 端口被 Hyper-V 随机保留（占用）的问题. *Tech Trends*. Retrieved from https://zhaoji.wang/solve-the-problem-of-windows-10-ports-being-randomly-reserved-occupied-by-hyper-v/

[2]Hung Yi. (2020, August 31). WSL2, Hyper-V & Reserved Ports. Tech Trend. Retrieved from https://hungyi.net/posts/wsl2-reserved-ports/
