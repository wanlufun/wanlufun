---
title: 解决 IDEA 终端输出中文乱码问题
tags:
  - IDEA
abbrlink: e11ed01
date: 2024-01-11 20:27:16
cover: https://images.wanlu.fun/picgo/202401112045144.png
---

## 问题背景

在 Windows 平台下，在日常的开发过程中，我们常常会遇到输出中文乱码问题，这是由于 Windows 系统编码不是 **UTF-8** 而导致的（通常我们在 IDEA 中一般都是以 UTF-8 编码去编辑文件的）。在地区为中国的情况下， Windows 平台默认系统编码为 **GBK** ，由于解码或编码使用的字符集不一样，从而会导致乱码问题的出现。

以下主要提供两种方式去解决解决以上问题：

1. 修改程序**读取/写入**使用的字符集
2. 修改 Windows 系统默认编码（如果您是一名游戏爱好者，并不推荐此项，可能会导致游戏无法打开的现象）



## 解决方法

### 方法一

![image-20240111204546094](https://images.wanlu.fun/picgo/202401112045144.png)

<div style="text-align: center;">图 1 </div>

打开**设置**再找到**文件编码**，按照以上图片修改为我的配置。

![image-20240111204654732](https://images.wanlu.fun/picgo/202401112046819.png)

<div style="text-align: center;">图 2 </div>

然后找到 HELP 栏中，找到 `Edit Custom VM Options` 在里面换行添加 `-Dfile.encoding=UTF-8` （设置了一个 Java 系统属性，它指定 JVM 在处理输入和输出操作时所使用的字符编码。默认为系统编码），然后重启 IDEA 即可。



### 方法二

![image-20240111203524971](https://images.wanlu.fun/picgo/202401112035042.png)

点击 Additional clocks 。

![image-20240111203608956](https://images.wanlu.fun/picgo/202401112036012.png)

点击 Date and Time 。

![image-20240111203754061](https://images.wanlu.fun/picgo/202401112037151.png)

点击 Change date and time 。

![image-20240111203816478](https://images.wanlu.fun/picgo/202401112038526.png)

点击 Change calendar settings 。

![image-20240111203830423](https://images.wanlu.fun/picgo/202401112038454.png)

点击 Administrative 。

![image-20240111203849017](https://images.wanlu.fun/picgo/202401112038038.png)

点击 Change system locale 。



![image-20240111203902300](https://images.wanlu.fun/picgo/202401112039372.png)

勾选 Bete: Use Unicode UTF-8 for worldwide language support 。

重启电脑，电脑默认编码变设置为 UTF-8 了。



### 方法三（手动狗头）

换 mac 电脑 / 换为 Linux 系统，它们默认系统编码均为 UTF-8 。



## 参考文献

[1]小哈. (2022, December 24). IDEA 控制台中文乱码 4 种解决方案. *Tech Trends*. Retrieved from https://www.quanxiaoha.com/idea/idea-chinese-garbled-code.html
