---
title: git 三种合并方式（rebase / merge / squash）
tags:
  - git
abbrlink: 46047e6a
date: 2024-02-01 22:14:08
---

## 问题背景

在使用 Git （分布式版本控制系统），往往不会直接在主分支上面直接开发，而是新建一个分支进行开发。

那么当我们在新分支上面完成一个功能时，便需要合并到主分支。



## 三种合并

* merge
* rebase and merge
* squash and merge



### 初始化

![diagram-export-2-1-2024-2_03_53-PM](https://images.wanlu.fun/picgo/202402011404993.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 1 </div>

图1，初始两个分支的状态，现基于此对合并（merge）和变基（rebase）操作讲解。

存在两个分支，当前状态为：

* master : A -> B -> C -> D -> E
* feature : A -> B -> C -> F -> H



### merge

![diagram-export-2-1-2024-2_30_41-PM](https://images.wanlu.fun/picgo/202402011430694.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 2 </div>

图 2 ，当 master 分支与 feature 分支都存在新的 commit 时，那么在 master 分支使用 `git merge feature` 会将 master 与 Feature 的最新 commit (E / H) 打包，在 master 分支提交为一个新的 commit (G)。

   

### Rebase

![diagram-export-2-1-2024-2_09_32-PM](https://images.wanlu.fun/picgo/202402011409749.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 3 </div>

图 3 ，当在 feature 分支使用 `git rebase master` ，会将 feature 分支的**基**变到 master 分支上。

注意：新 commit (F / H) 对比与旧提交，如图3 黄色的部分，其 sha 值不一致，说明 git rebase 是创建了新的提交。 ![diagram-export-2-1-2024-2_12_06-PM](https://images.wanlu.fun/picgo/202402011412453.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 4 </div>

图4，当在 master 分支使用 `git rebase feature` ，会将 master 分支的**基**变到 feature 分支上。



### Rebase and merge

![diagram-export-2-1-2024-2_14_59-PM](https://images.wanlu.fun/picgo/202402011415360.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 5 </div>

图 5  是由图 3 ，进行 merge 操作实现的，这是 merge 操作的**另一种情况** ：当 master 相对于 feature 没有新的提交时，在 master 分支上使用 `git merge feature` 会直接将 master 分支指向 feature 分支。



### Squash and merge

![diagram-export-2-1-2024-2_20_24-PM](https://images.wanlu.fun/picgo/202402011420451.png)

<div style="text-align: center; font-size: 80%; opacity: 0.5;">图 6 </div>

squash 顾名思义是压缩，图 6 将 新分支的 commit (F / H) 进行打包压缩在与 master 分支进行 merge 操作。

​       

## 优缺点

|  操作  |                优点                |                缺点                |
| :----: | :--------------------------------: | :--------------------------------: |
| merge  |     简单直观<br />保留完整历史     | 合并提交冗余<br />分支历史不够整洁 |
| rebase | 整洁的分支历史<br />清晰的提交历史 | 改变提交历史<br />容易产生合并冲突 |
| squash |           清晰的提交历史           |   丢失提交信息<br />破坏历史信息   |

