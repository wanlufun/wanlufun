---
title: CentOS 7.6 安装 Docker 与 Compose
tags:
  - Docker
  - Linux
abbrlink: 65063c20
date: 2024-03-23 16:29:58
cover: https://images.wanlu.fun/picgo/202403231518284.png
---

## Docker

Docker 是一个开源的平台，用于开发、部署和运行应用程序。

Docker 的主要作用包括：

1. **应用程序打包和交付：** Docker 允许开发人员将应用程序及其依赖项打包到一个称为镜像的容器中。这些镜像可以轻松地在不同的环境中部署和运行，确保应用程序的一致性和可移植性。
2. **环境隔离：** Docker 容器提供了一个隔离的运行环境，使得应用程序之间相互独立，并且不会相互影响。这种隔离性使得容器更加安全，并且可以避免由于应用程序之间的冲突而导致的问题。
3. **快速部署和扩展：** Docker 容器可以快速部署和启动，因为它们是轻量级的，并且可以在几秒钟内启动。这使得应用程序的部署和扩展变得更加简单和高效。
4. **持续集成和持续部署（CI/CD）：** Docker 可以与持续集成和持续部署工具集成，如 Jenkins、GitLab CI 等，使得自动化构建、测试和部署变得更加容易。
5. **微服务架构支持：** Docker 容器适用于微服务架构，每个微服务可以打包到一个独立的容器中，并且可以独立部署和扩展，从而提高了系统的灵活性和可维护性。

## 更新 yum 源

```shell
sudo yum update -y
```

## 卸载残留 Docker

```shell
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
```

## 安装依赖

```shell
sudo yum -y install gcc gcc-c++ yum-utils
```

## 添加 Docker 阿里仓库源

```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

- 第一行命令：添加阿里云 Docker CE 仓库，使系统能够安装 Docker CE。
- 第二行命令：替换系统默认的 CentOS 基础仓库，使系统能够从阿里云镜像源下载软件包，提高下载速度和稳定性。

## 安装 Docker

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

## 查看 Docker 版本

```
	docker --version
```

![image.png](https://images.wanlu.fun/picgo/202403231518284.png)
出现以上信息，说明安装完成。

## 安装 Docker Compose

Docker Compose 是 Docker 官方提供的一个工具，用于定义和管理多个 Docker 容器的应用程序。它允许您使用一个 YAML 文件来定义应用程序的服务、网络、卷等，并通过简单的命令来启动、停止和管理整个应用程序。Docker Compose 简化了多个容器之间的交互和管理，使得开发人员能够更加轻松地构建和部署复杂的应用程序。

Docker Compose 分为 v1 和 v2 两个版本，v1 版本使用 `docker-compose` 命令，而 v2 版本作为 Docker 插件通过使用 `docker compose` 使用。

### V1

> [!warning]
> 从 2023 年 7 月起，Compose V1 停止接收更新，并且不再包含在新的 Docker Desktop 版本中。Compose V2 取代了它，并且已经集成到所有当前的 Docker Desktop 版本中。

1. 下载 v1 版本 docker-compose

```shell
curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

2. 添加执行权限

```shell
sudo chmod + x /usr/local/bin/docker-compose
```

3. 查看版本

```shell
docker-compose version
```

### V2

前往 https://github.com/docker/compose/releases 查看最新版 docker compose 版本，替换下面 2.26.0

1. 下载插件

```shell
mkdir -p /usr/local/lib/docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.26.0/docker-compose-linux-x86_64 -o /usr/local/lib/docker/cli-plugins/docker-compose
```

2. 为插件添加执行权限

```shell
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

3. 查看 docker compose 版本

```shell
docker compose version
```

![image.png](https://images.wanlu.fun/picgo/202403231602946.png)

## 进程守护

- 重载

```shell
sudo systemctl daemon-reload
```

- 开机自启

```shell
sudo systemctl enable docker
```

- 重启

```shell
sudo systemctl restart docker
```

- 启动

```shell
sudo systemctl start docker
```

- 停止

```shell
sudo systemctl stop docker
```

- 查看状态

```shell
sudo systemctl status docker
```

## 参考文献

[1] 指剑. (2022.04.01). 基于 CentOS 7.6 的 Docker 新手教学. _Tech Trends_. Retrieved from https://developer.aliyun.com/article/880931
[2] JackieDYH. (2022.06.20). Centos7.6 版本-安装 Docker 服务-过程记录. _Tech Trends_. Retrieved from https://blog.51cto.com/jackiehao/5395330
[3] Eniso. (2019.02.27). # CentOS 7 安装 Docker 和 Docker-Compose. _Tech Trends_. Retrieved from https://www.jianshu.com/p/7d9ff93bc89e
[4] Docker Docs. _Tech Trends_. Retrieved from https://docs.docker.com/compose/install/linux/#install-the-plugin-manually
