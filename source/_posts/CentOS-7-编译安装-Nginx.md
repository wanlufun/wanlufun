---
title: CentOS 7 编译安装 Nginx
tags:
  - Linux
  - Nginx
abbrlink: 7bbac0ee
date: 2024-03-18 21:42:57
cover: https://images.wanlu.fun/picgo/202403182054317.png
---
## 背景

一开始使用 docker 搭建了一个 web 服务器，但是由于 docker 不太方便的部署 TLS 证书，故使用 Nginx 做反向代理，实现 https 连接。

## 下载 Nginx 源码包

Nginx 主要提供两个版本，分别为：Mainline version 与 Stable version，前面是主线版本，有新的特性会在此版本中体现，但是 BUG 的出现的可能性也会比 稳定版本高，故以下使用写这篇博客下最新的稳定版作为演示。
![image.png](https://images.wanlu.fun/picgo/202403182054317.png)

由红框中指出的是 Linux 下，应该下载的源码包，获取它的下载链接。
然后在服务器上先进入系统的源代码存放处，在下载源码包，解压并进入：

```bash
cd /usr/local/src
curl -LO https://nginx.org/download/nginx-1.24.0.tar.gz
tar -zxvf nginx-1.24.0.tar.gz
cd nginx-1.20.2
```

## 安装依赖包

1. 安装编译环境

```bash
yum -y install gcc gcc-c++

```

2. 如果要使用 rewrite、ssl 与 gzip 功能，则需要 pcre、openssl、zlib 依赖包

```bash
yum -y install pcre pcre-devel openssl openssl-devel zlib zlib-devel
```

## 编译

```bash
./configure --prefix=/usr/local/nginx \
	--with-http_ssl_module \
	--with-http_v2_module \
	--with-http_realip_module

```

**命令解释**

| 配置选项                 | 功能                           |
| ------------------------ | ------------------------------ |
| -prefix                  | 指定安装路径                   |
| --with-http_ssl_module   | 支持 HTTPS 协议                |
| –with-http_v2_module     | 支持 HTTP/2                    |
| –with-http_realip_module | 从指定请求头获取客户端 IP 地址 |

执行完上面的命令后，可以开始进行正式的编译了

```bash
make && make install
```

如果你的服务器配置较高，可以使用多线程编译 在 make 后添加 -j3 参数

```bash
make -j3 && make install
```

## 添加环境变量

如果使用 bash shell，则使用以下命令：

```bash
echo 'export PATH="/usr/local/nginx/sbin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

如果使用 zsh shell，则使用：

```
echo 'export PATH="/usr/local/nginx/sbin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## 添加守护

使用 vim 在 /etc/systemd/system/ 文件夹下，添加 nginx.service。`vim /etc/systemd/system/nginx.service`
然后将如下所示的内容复制粘贴到该文件中并保存（vim 编辑器先按 ESC 再输入  `:wq`）：

```ini
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID

[Install]
WantedBy=multi-user.target
```

运行以下命令，将 systemd 重新读取新的配置内容

```bash
systemctl daemon-reload
```

以下命令，都是用来管理 Nginx 服务的：
随系统启动服务

```bash
systemctl enable nginx
```

启动 Nginx

```bash
systemctl start nginx
```

停止 Nginx

```bash
systemctl stop nginx
```

重载 Nginx

```bash
systemctl reload nginx
```


## 查考文献
[1] OSFERE. (2022.2.2). 如何在 CentOS 上编译安装及配置最新版 Nginx. *Tech Trends*. Retrieved from [https://osfere.com/linux/how-to-build-install-and-configure-latest-nginx-on-centos](https://osfere.com/linux/how-to-build-install-and-configure-latest-nginx-on-centos)
