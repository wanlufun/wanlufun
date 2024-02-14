---
title: WebStorm 终端找不到命令
abbrlink: 5c6e3e5f
date: 2024-02-14 21:14:27
tags:
cover: https://images.wanlu.fun/picgo/202401301155742.png
---

通过 npm 命令下载了 yarn ，但可以在 windows 的终端（ cmd ）找到命令。

![image-20230130223811587](https://images.wanlu.fun/picgo/202401301155742.png)

但使用 WebStorm 的 terminal 时候，却找不到命令。

![image-20230130224008607](https://images.wanlu.fun/picgo/202401301155560.png)



## 解释原因

由于开机第一次启动 WebStorm 时（其他的 Jetbrains 的 IDE 也一样），WebStorm 会根据当前系统的 Path 将它序列化到自己的配置文件中，如果不重启电脑，重启 WebStorm 还是会重新加载之前的配置文件，而**之后**的增加的环境变量不会读取，也就识别不到命令了。



## 解决方法

1. 重启电脑
2. 手动在 WebStorm 添加 Path ：`set PATH=××××`

