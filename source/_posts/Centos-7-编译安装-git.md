---
title: Centos 7 编译安装 Git
tags:
  - Linux
  - Git
abbrlink: 2a1cfec5
date: 2024-03-18 21:40:24
cover: https://images.wanlu.fun/picgo/202403172036673.png
---
## 背景来源
为什么要安装新版本呢？
因为无聊，哈哈哈，其实也不是，本来开始装 Neovim 的 lazy.nvim 的插件包管理器时，有一个下载配置是从 Github 克隆下一个仓库：`git clone --filter=blob:none https://github.com/folke/lazy.nvim.git --branch=stable`，但是由于 CentOS 7 源中的 Git 包版本太低，不支持 `filter` 这个选项，故没有办法，就选择了编译安装 Git 。其实也可以去掉 `filter` 选项，这样就不用安装新版本的 Git 了，哈哈哈。
## 删除旧版本 Git

`yum autoremove -y git`

## 安装依赖包

`yum install -y gcc zlib-devel autoconf libcurl-devel curl-devel`

## 下载 Git 源代码

进入 [Git 官方 Github 仓库](https://github.com/git/git) 中 tags 找到最新版的源码。
目前最新版的 v2.44.0 的版本。
`wget https://github.com/git/git/releases/tag/v2.44.0`
`tar zxvf v2.44.0.tar.gz`
`cd git-2.44.0`

## 检验相关依赖，设置安装路径

1. 校验 Git 依赖是否完备。
   `make configure`
2. 配置编译安装的路径
   ./configure --prefix=/usr/local/git

## 编译安装

`make && make install`
如果是多核，可以采用多线程编译，加快编译速度。
`make -j3 && make install`

## 添加 Git 环境变量

如果是普通的 shell 终端，就执行：`echo "export PATH=$PATH:/usr/local/git/bin" >> ~/.bashrc`
如果是 zsh 终端的话，就执行：`echo "export PATH=$PATH:/usr/local/git/bin" >> ~/.zshrc`

## 重新加载配置文件

`source ~/.bashrc 或 source ~/.zshrc`

## 查看版本号

`git --version`

![image.png](https://images.wanlu.fun/picgo/202403172036673.png)

## 参考文献
[1] PasseRR. (2022-03-01). CentOs升级Git版本. *Tech Trends*. Retrieved from https://www.xiehai.zone/2022-03-01-centos-upgrade-git.html
