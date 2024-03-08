---
title: SSH 免密码登录
tags:
  - 运维
abbrlink: caad5cb1
date: 2024-03-08 22:14:42
cover: https://images.wanlu.fun/picgo/202403082150810.png
---

## 背景说明

SSH 登录有两种方式：

1. 密码登录
2. 密钥登录

SSH 中常常使用两种算法 RSA 与 Ed25519 ，以前存在其它算法，但是被正式证明不安全，现在普遍都是这两种算法。

Ed25519 是一种椭圆曲线加密算法，具有较高的安全性和效率，并且比 RSA 更安全些，以下使用 Ed25519 算法生成密钥对，以实现 SSH 免密码登录。



## 生成密钥对

```bash
ssh-keygen -t ed25519 -f id_ssh  -C "mail"

// 参数说明
-t: 指定要生成的密钥类型。在本例中，-t ed25519 表示生成 Ed25519 密钥。
-f: 指定密钥文件的名称。在本例中，-f id_ssh 表示将生成的文件命名为 id_ssh。
-C: 添加注释到密钥文件中。在本例中，-C "mail" 表示添加注释 "mail" 到密钥文件中。
```

![image-20240308215042703](https://images.wanlu.fun/picgo/202403082150810.png)

输入以上命令后，会提示是否为密钥进行加密，直接 Enter 就是不对密钥进行加密。

![image-20240308215223873](https://images.wanlu.fun/picgo/202403082152933.png)

然后在当前路径下，便会存在与之相关的两个文件

* id_ssh : 私钥
* id_ssh.pub : 公钥



## 向当前用户添加公钥

将公钥复制到用户根目录下的 .ssh/authorized_keys 文件中。

```bash
cat id_ssh.pub >> ~/.ssh/authorized_keys
```



## 修改 sshd 配置

```bash
vim /etc/ssh/sshd_config
```

将 PasswordAuthentication 修改为 no，PubkeyAuthentication 修改为 yes 

![image-20240308220043575](https://images.wanlu.fun/picgo/202403082200619.png)

上图参数说明：

* **PermitRootLogin**：是否允许 root 用户通过 SSH 登录。
* **PasswordAuthentication**：是否允许使用密码进行 SSH 登录。
* **UseDNS**：是否在 SSH 登录过程中使用 DNS 解析。
* **RSAAuthentication**：是否允许使用 RSA 密钥进行 SSH 登录。
* **PubkeyAuthentication**：是否允许使用公钥进行 SSH 登录。



## 注意事项

* 如果关闭 UseDNS 将无法使用域名进行登录服务器。

* .ssh 文件夹的权限为：700

* authorized_keys 文件权限为：600

  ```bash
  chmod 700 .ssh
  chmod 600 authorized_keys
  ```

  
