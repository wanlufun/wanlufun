---
title: 使用 k8s 搭建 wordpress
tags:
  - k8s
abbrlink: 97b2d010
date: 2024-02-03 22:22:27
cover: https://images.wanlu.fun/picgo/202402032004899.png
---

[k8s](https://kubernetes.io/) 是容器编排系统，使用 K8s 可以做到 **自动扩缩** 、 **故障转移**，等等优秀的特性，被常常在企业中使用。

搭建一个简易的 k8s 可以使用 [k3s](https://k3s.io/) 。

* 轻量级（0.5G 就可以使用）
* 拥有大部分的 k8s 功能
* 支持多节点

具体搭建过程及更多信息可以参考官网。

![image-20240203200458558](https://images.wanlu.fun/picgo/202402032004899.png)

## 部署思路

wordpress 部分

搭建 wordpress ，需要了解搭建一个完整的 wordpress 服务并且对外开放，所需要的服务。

1. wordpress 服务端（提供 wordpress 后端服务、配置、插件等等）
2. Mysql （提供存储数据的能力，比如：文章、评论等等）

根据以上信息，wordpress 服务端和 Mysql 需要存储一些信息，故需要对数据进行持久化。

k8s 中卷常见的类型：

* hostpath 。无法被多个节点中 pods 读写
* 网络卷（比如： NFS）。可以被多个节点内的 pods 读写。**推荐**



在 k8s 中，一个 pod 可以包含多个 container ，那么对于 wordpress 与 mysql 也可以吗？

可以，但是不推荐。

* 耦合性强
* 博客网站多为多读少写，瓶颈在 IO ，故可能只需要动态地对 mysql 进行扩缩，而不需要对 wordperss 进行扩散。



最后需要对外暴露服务，故需要 service 对 pod 指定一个访问接口。





mysql 部分：

* 对于 mysql 需要密码等敏感信息，故可以使用 k8s 中的 secret 去保存密码，但是 secret 中是使用 base64 进行保存的，并不是特别安全，可以使用其他更安全的方式进行存储。

* 需要使用持久卷去存储数据。
* 使用 service 暴露端口，提供给内部 pod (wordpress) 访问，通过 k8s 的 DNS 服务访问 myslq 。



## 编写 wordpress 部署 yaml

service : 对集群内部暴露 80 端口给集群内部访问，通过 LoadBalancer 暴露端口给外部访问。

```yaml
apiVersion: v1
kind: Service
metadata:
	name: wordpress
	namespace: wordpress
	labels:
		app: wordpress
spec:
	ports:
		- port: 80
	selector:
		app: wordpress
		tier: fontend
	type: LoadBalancer
```

PersistentVolumeClaim : 申请 20G 存储空间

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
	name: wp-pv-claim
	namespace: wordpress
	labels:
		app: wordpress
spec:
	accessModes:
		- ReadWriteOnce
	resources:
		requests:
			storage: 20Gi
```

Deployment : 部署 wordpress 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  namespace: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: frontend
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: frontend
    spec:
      containers:
      - image: wordpress:laest
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        - name: WORDPRESS_DB_USER
          value: wordpress
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - name: wordpress-persistent-storage
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage
        persistentVolumeClaim:
          claimName: wp-pv-claim
```



## 编写 mysql.yaml

secret : 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-pass
  namespace: wordpress
type: Opaque
data:
  password: <your-base64-encoded-password>
```

service : 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
  namespace: wordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 3306
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
```

PersistentVolumeClaim : 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  namespace: wordpress
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

Deployment : 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-mysql
  namespace: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
        - image: mysql:8.0
          name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password
            - name: MYSQL_DATABASE
              value: wordpress
            - name: MYSQL_USER
              value: wordpress
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-pass
                  key: password
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```



## 部署验证

将以上两个配置，分别组合为 wordpress-frontend.yaml / wordpress-mysql.yaml 文件。

```bash
kubectl apply -f wordpress-frontend.yaml
kubectl apply -f wordpress-mysql.yaml
```

k8s 将会开始部署。由于镜像可能有点慢，等待时间可以会有点久。



可以通过以下命令进行验证：

```yaml
kubectl get secrets -n wordpress
```

查看为 mysql 创建的 secret 是否存在。

![image-20240203212107374](https://images.wanlu.fun/picgo/202402032121589.png)

```yaml
kubectl get pvc -n wordpress
```

查看为 wordpress 与 mysql 创建的两个 pvc 是否存在。

![image-20240203212123308](https://images.wanlu.fun/picgo/202402032121702.png)

```yaml
kubectl get pods -n wordpress
```

查看 wordpress 与  mysql 两个服务是否在运行。

![image-20240203212141855](https://images.wanlu.fun/picgo/202402032121112.png)

```yaml
kubectl get services -n wordpress
```

查看两个 service 是否操作。

![image-20240203212159910](https://images.wanlu.fun/picgo/202402032122186.png)



当两个 pod 都在运行时，可以通过 `kubectl get services -n wordpress` 查看进行服务信息，如上图，名为 wordpress 栏的 PORT(s) 列，其中 30122 为外部地址访问的端口。

我使用云服务器部署该应用，故可以通过 http://<pulic-ip>:30122 访问 wordpress 初始页面。

![image-20240203212435359](https://images.wanlu.fun/picgo/202402032124561.png)
