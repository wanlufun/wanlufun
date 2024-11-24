---
title: Docker 中 RUN、CMD 和 ENTRYPOINT 的区别解析
abbrlink: 9312980b
date: 2024-11-24 13:55:43
tags:
---

## 背景

Docker 凭借强大的容器化能力，为开发者提供了一个灵活的环境，可以轻松地打包、分发和运行应用程序。然而，对于初学者来说，某些 Docker 指令的相似语法常常会引发混淆。本文将深入解析 RUN、CMD 和 ENTRYPOINT 这三者之间的区别，并通过清晰的示例帮助您轻松掌握这个 Docker 指令三剑客。



## 速览

1. **RUN**：在新的一层中执行命令，并生成一个新的镜像。通常用于安装软件包。
2. **CMD**：定义容器启动时的默认命令或参数，启动时可以被覆盖。
3. **ENTRYPOINT**：将容器配置为一个可执行程序的形式运行。



接下来，我们将逐一解析这些指令，深入理解它们的作用及使用场景。



## Shell 表达式 vs. Exec 表达式

在探讨具体指令之前，需要了解两种语法形式的区别：**Shell 表达式**和**Exec 表达式**。它们的选择会直接影响指令的执行方式。


### Shell 表达式

格式：

```
<指令> <命令>
```

**示例：**

```
RUN apt-get -y update  
CMD echo "Hello world"  
ENTRYPOINT echo "Hello world"  
```

在 **Shell 表达式**中，指令通过 `/bin/sh -c` 调用，底层会执行标准的 Shell 处理逻辑。例如，在以下 Dockerfile 中：

```dockerfile
ENV variable Tutorials  
ENTRYPOINT echo "$variable"  
```

当以 `docker run -it <image>` 启动容器时，会输出：

```
Tutorials  
```

变量名称被正确替换为其值。

------

### Exec 表达式

格式：

```
<指令> ["可执行文件", "参数1", "参数2", ...]
```

**示例：**

```
RUN ["apt-get", "install", "python3"]  
ENTRYPOINT ["/bin/echo", "Tutorials"]  
CMD ["/bin/echo", "Tutorials"]  
```

在 **Exec 表达式**中，直接调用可执行文件，**不会进行 Shell 处理**。例如：

```dockerfile
ENV variable Tutorials  
ENTRYPOINT ["/bin/echo", "$variable"]  
```

当以 `docker run -it <image>` 启动容器时，输出如下：

```
$variable  
```

变量名并未被替换为其值。


## 如何运行 Bash？

如果需要使用 Bash（或其他解释器，如 Python），建议使用 Exec 表达式，通过 `/bin/bash` 执行。此时可以正常进行 Shell 处理。例如：

```dockerfile
ENV variable Tutorials  
ENTRYPOINT ["/bin/bash", "-c", "echo $variable"]  
```

当以 `docker run -it <image>` 启动容器时，输出：

```
Tutorials  
```


## **RUN 指令**

**RUN** 通常用于在镜像构建过程中安装软件包或执行某些操作。每次执行 **RUN** 指令时，都会添加一个新层到镜像中。Dockerfile 中可以包含多个 **RUN** 指令。

**两种形式：**

1. Shell 表达式：

   ```
   RUN <命令>
   ```

2. Exec 表达式：

   ```
   RUN ["可执行文件", "参数1", "参数2"]
   ```

**示例：**

```dockerfile
RUN apt-get update && apt-get install -y \  
  vim \  
  tzdata \  
  git \  
  mercurial \  
  subversion  
```


## **CMD 指令**

**CMD** 指令定义容器启动时默认执行的命令。可以在运行容器时通过命令覆盖它。如果 Dockerfile 中有多个 **CMD** 指令，只有最后一个生效。

**三种形式：**

1. Exec 表达式（推荐）：

   ```
   CMD ["可执行文件", "参数1", "参数2"]
   ```

2. 附加参数形式（与 ENTRYPOINT 配合使用）：

   ```
   CMD ["参数1", "参数2"]
   ```

3. Shell 表达式：

   ```
   CMD 命令 参数1 参数2
   ```

**示例：**

```dockerfile
CMD echo "Hello world"
```

运行：

```
docker run -it <image>  
```

输出：

```
Hello world  
```

但如果运行以下命令：

```
docker run -it <image> /bin/bash  
```

**CMD** 会被忽略，直接启动 Bash：

```
root@7de4bed89922:/#  
```


## **ENTRYPOINT 指令**

**ENTRYPOINT** 用于将容器配置为一个固定的可执行任务，与 **CMD** 的不同之处在于它不会被启动时的命令行参数完全覆盖。**ENTRYPOINT** 的参数始终生效，而 **CMD** 参数可被替代。

**两种形式：**

1. Exec 表达式（推荐）：

   ```
   ENTRYPOINT ["可执行文件", "参数1", "参数2"]
   ```

2. Shell 表达式：

   ```
   ENTRYPOINT 命令 参数1 参数2
   ```

**示例：**

```dockerfile
ENTRYPOINT ["/bin/echo", "Tutorials"]  
CMD ["Docker"]  
```

运行：

```
docker run -it <image>  
```

输出：

```
Tutorials Docker  
```

运行：

```
docker run -it <image> DockerTutorials  
```

输出：

```
Tutorials DockerTutorials  
```


## **总结**

1. **RUN 指令** 用于构建镜像，执行构建时所需的任务。
2. **CMD 指令** 提供容器默认的运行命令，具有灵活性，可被覆盖。
3. **ENTRYPOINT 指令** 保证固定任务的执行，与 **CMD** 配合更具弹性。

在编写 Dockerfile 时，选择合适的指令形式和表达方式，可以提高镜像的复用性和易维护性。


## 参考文献

内容参考自：https://codewithyury.com/docker-run-vs-cmd-vs-entrypoint/ 。
