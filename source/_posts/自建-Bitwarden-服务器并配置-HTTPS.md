---
title: 自建 Bitwarden 服务器并配置 HTTPS
tags:
  - Bitwarden
  - Nginx
  - Docker
abbrlink: 4b35ba1
date: 2024-03-23 16:35:47
cover: https://images.wanlu.fun/picgo/202403231441477.png
---

## 背景

我时常因为要记住各种平台的账户密码而担忧，因为怕忘记，然后就想找一款可以保存密码的软件，市面上有 1password ，我使用过它，它确实好用，我通过 Github 学生认证包，免费尝试了一年，但是续费比较贵，所有我选择买一台服务器，自建服务器，数据在自己手里更放心。

## Bitwarden

Bitwarden 是一款开源的密码管理平台，并且它提供跨平台的支持。

- 开源的代码
- 云端同步
- 项目类型，如登录信息、安全笔记、信用卡、身份
- 保管库数据的端到端加密
- 密码历史记录，可以在登录信息中查阅以前的密码
- 与其他 Bitwarden 用户安全地分享保管库中的记录
- 自动填写登录信息到网站和其他应用程序
- 密码生成器
- 2FA
- 文件作为附件
- TOTP 密钥存储及代码生成器
- 数据泄露报告和密码暴露检查
- 跨平台客户端
- 自行部署 Bitwarden 服务器

## 使用 docker 搭建服务端

> [!info]
> 如何安装 Docker 可以参考：[CentOS 7.6 安装 Docker 与 Compose](https://www.wanlu.fun/65063c20.html)

由于官方提供的 Docker 镜像，要求服务器的内存大于 3G，所以我们使用第三方开源的服务端 https://github.com/dani-garcia/vaultwarden ，它由于使用 Rust 编写，性能极高，并且占用内存极低，只需要 128M 左右，以及解锁官方大部分的高级功能和提供 Docker 镜像，只需要几条命令就可以完成部署。

### 拉取镜像

```shell
docker pull vaultwarden/server:latest
```

### 部署

由于我是部署给自己使用，我会关闭服务端的注册功能，所以会有一个先简易部署，进行账户注册，然后再次部署。

#### 简易部署

先通过简易命令进行部署服务端：

```shell
docker run -d --name vaultwarden \
	-v /root/bitwarden/:/data/ \
	-p 11000:80 \
	vaultwarden/server:latest
```

先访问该服务端，然后通过 _https://公网 ip:11000_ 进行访问，然后注册账户。**记住自己注册的账户**

#### 删除容器

1. 找到通过简易部署对应的容器

```shell
dcoker ps
```

2. 找到对应的容器 id 为 f511495b9da5
   ![image.png](https://images.wanlu.fun/picgo/202403231441477.png)

3. 停止该容器

```shell
docker stop f511495b9da5
```

4. 删除该容器

```shell
docker rm f511495b9da5
```

#### 再次部署

使用 `openssl rand -base6` 生成 token，填入下方 \<token> 中。

```shell
docker run -d --name vaultwarden \
	-e SIGNUPS_ALLOWED=false \
	-e INVITATIONOS_ALLOWED=false \
	-e ADMIN_TOKEN='<token>' \
	-e WEBSOCKET_ENABLED=true \
	-v /root/bitwarden/:/data/ \
	-p 127.0.0.1:11000:80 \
	vaultwarden/server:latest
```

**注**：[参数解释](https://github.com/dani-garcia/vaultwarden/wiki)

- `-p 127.0.0.1:11000:80` 为安全只提供内网访问，通过 nginx 反向代理，进行 HTTPS 配置。

| 参数                 | 解释                                                        |
| -------------------- | ----------------------------------------------------------- |
| SIGNUPS_ALLOWED      | 允许注册                                                    |
| INVITATIONOS_ALLOWED | 是否允许组织邀请注册                                        |
| ADMIN_TOKEN          | Admin 页面后台密码，推荐使用 `openssl rand -base64 48` 生成 |
| WEBSOCKET_ENABLED    | 是否开启 WebSocket                                          |

### 搭建 HTTPS

> [!info]
> 编译安装 Nginx 可以参考 [CentOS 7 编译安装 Nginx](https://www.wanlu.fun/7bbac0ee.html)

在 Nginx 配置目录中 nginx.conf 文件的 http 块中添加以下内容：

```conf
map $http_upgrade $connection_upgrade {
	default upgrade;
	'' close;
}

server {
	listen 443 ssl;
	server_name <domain-name>;

	ssl_certificate <cert-path>;
	ssl_certificate_key <private-cert-path>;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1.1 TLSv1.2;
	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
	ssl_prefer_server_ciphers on;

	location / {
		proxy_pass http://127.0.0.1:11000;
		proxy_redirect off;

		proxy_http_version 1.1;
		proxy_set_header        Host    $host;
		proxy_set_header        X-Real-IP       $remote_addr;
		proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_read_timeout 3600s;

		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection $connection_upgrade;
	}
}
```

然后通过域名访问，就可以通过 https 传输信息了。



**注**：配置解释

- 配置 Websocket
```shell
map $http_upgrade $connection_upgrade {
	default upgrade;
	'' close;
}

location / {
	…………
	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection $connection_upgrade;
	…………
}
```

