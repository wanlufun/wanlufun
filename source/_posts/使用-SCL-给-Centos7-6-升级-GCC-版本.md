---
title: 使用 SCL 给 Centos7.6 升级 GCC 版本
tags:
  - Linux
abbrlink: cfc49606
date: 2024-03-20 23:17:26
cover: https://images.wanlu.fun/picgo/202403202301022.png
---

## 什么是 SCL

SCL 软件集是为了给 RHEL / CentOS 用户提供一种方便、安全的安装和使用应用程序和运行时环境的多个（而且可能是更新的）版本的方式，同时避免把系统搞乱。

## 背景

由于 CentOS 和 RHEL 为追求系统稳定，所以 CentOS 7.6 默认的 gcc 版本是 4.8.5，由于它不太支持最新的语法，所以在编译一些软件的时候会出错，对开发人员极度不友好。
![image.png](https://images.wanlu.fun/picgo/202403202301022.png)

## 安装 SCL

```bash
yum install -y centos-release-scl scl-utils-build
```

## 安装工具集

devtoolset 是一个工具集，它受 Red Hat 的支持维护，包含 GCC，GDB，Make 等等常用的开发和调试工具。其存在的目的就是为了让开发者能够使用一些比较新的工具，从而提升开发效率，缩短开发时间。
devtoolset 在 CentOS 和 RHEL 上都可以使用。安装 devtoolset 后，它并不会将系统内默认的工具覆盖掉，而是额外安装在`/opt`目录下，并且默认状态下也不会启用，用户可以通过命令或脚本使用它们。

> [!warning] 
>
> 从 CentOS8/RHEL8 开始，devtoolset 更名为 gcc-toolset

```bash
yum install devtoolset-8
```

## 启用环境

如果你是 bash 用户，则运行以下命令：

```bash
scl enable devtoolset-8 bash
```

如果你是 zsh 用户，则运行以下命令：

```bash
scl enable devtoolset-8 zsh
```

查看版本：

```bash
gcc --version
```

![image.png](https://images.wanlu.fun/picgo/202403202310402.png)

## devtoolset 与 gcc 版本对应

```text
devtoolset-3 对应 gcc4.x.x版本
devtoolset-4 对应 gcc5.x.x版本
devtoolset-6 对应 gcc6.x.x版本
devtoolset-7 对应 gcc7.x.x版本
devtoolset-8 对应 gcc8.x.x版本
devtoolset-9 对应 gcc9.x.x版本
devtoolset-10 对应 gcc10.x.x版本
```

## 参考文献

[1] 李拜六. (2022.2.13). CentOS/RHEL 通过安装 devtoolset 升级 GCC. _Tech Trends_. Retrieved from [https://www.imlb6.com/centos-rhel-install-devtoolset/#:~:text=devtoolset%20%E6%98%AF%E4%B8%80%E4%B8%AA%E5%B7%A5%E5%85%B7%E9%9B%86%E5%90%88,%E7%9A%84%E5%BC%80%E5%8F%91%E5%92%8C%E8%B0%83%E8%AF%95%E5%B7%A5%E5%85%B7%E3%80%82](https://www.imlb6.com/centos-rhel-install-devtoolset/#:~:text=devtoolset%20%E6%98%AF%E4%B8%80%E4%B8%AA%E5%B7%A5%E5%85%B7%E9%9B%86%E5%90%88,%E7%9A%84%E5%BC%80%E5%8F%91%E5%92%8C%E8%B0%83%E8%AF%95%E5%B7%A5%E5%85%B7%E3%80%82)
[2] 开源 Linux. (2023.9.8). # CentOS7/完美升级 gcc 版本方法. _Tech Trends_. Retrieved from [https://zhuanlan.zhihu.com/p/535657060](https://zhuanlan.zhihu.com/p/535657060)
