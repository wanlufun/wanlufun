---
title: windows 配置 Java 环境变量并解释原因
tags:
  - Java
abbrlink: 25cf84b6
date: 2024-02-14 21:10:48
cover: https://images.wanlu.fun/wordpress/20230122205010.png
---

## 一、安装 Java 8 （企业常用 Java8 如果对新技术感兴趣，也可安装新版本）

1. 下载 [oracle JDK 8](https://www.oracle.com/java/technologies/downloads/)
   ![](https://images.wanlu.fun/wordpress/20230122205010.png)
   目前在 [Oracle](https://www.oracle.com/) 官网下载老版本 JDK 需要账号才可以下载，账号资源可在网上寻找。

2. 安装 JDK8
   ![](https://images.wanlu.fun/wordpress/20230122205558.png)
   选择一个合适的安装位置，点击下一步即可。
   ![](https://images.wanlu.fun/wordpress/20230122205951.png)
   等待进度条，然后会让你安装 JRE 。
   ![](https://images.wanlu.fun/wordpress/20230122210714.png)
   选择一个合适的安装位置，点击下一步即可。
   ![](https://images.wanlu.fun/wordpress/20230122210839.png)
   等待安装。
   ![](https://images.wanlu.fun/wordpress/20230122210858.png)
   成功安装 JDK8 ，点击关闭即可。

3. 验证是否成功安装 JDK8
   打开 CMD ，输入 `java -version` 。显示以下信息，就安装好了。
   ![](https://images.wanlu.fun/wordpress/20230122211457.png)

注：安装JDK8会自动为你添加一条环境变量
![](https://images.wanlu.fun/wordpress/20230122211318.png)


## 二、配置环境变量

需要配置的环境变量有:

* JAVA_HOME
* Path


1. 打开系统属性，选择点击高级选项，并点击环境变量。
   ![](https://images.wanlu.fun/wordpress/20230122213034.png)
   点击新建。
   ![](https://images.wanlu.fun/wordpress/20230122213228.png)
   新建一条 JAVA_HOME ，并填入 JDK8 的路径，然后添加。
   ![](https://images.wanlu.fun/wordpress/20230122213321.png)
   然后点击 Path 
   ![](https://images.wanlu.fun/wordpress/20230122213534.png)
   选择新建，然后填入 `%JAVA_HOME%\bin`
   ![](https://images.wanlu.fun/wordpress/20230122213644.png)
   然后环境变量均以添加完成。

## 三、为什么要添加 JAVA_HOME 和 Path

系统变量可以看作为一个系统提供给程序的变量，提供程序指定文件的索引。

* JAVA_HOME ：
  1. 方便引用 `%JAVA_HOME%`
  1. 提供第三方程序 JAVA 目录的位置（约定好的事情）


* Path ：
  保存系统会去搜索的目录路径，如果你准备运行的程序不在当前目录，操作系统便会依次搜索 Path 环境变量中的目录，如果在这些目录中找到待运行的程序，操作系统便可以运行。

