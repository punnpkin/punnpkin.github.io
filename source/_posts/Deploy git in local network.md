---
layout:     post
title:      记录一下局域网部署 git 的过程
category:   Linux
tags:       git
---


首先安装依赖：

```shell
$ sudo apt update
$ sudo apt -y install git-core openssh-server openssh-client python-setuptools
```

然后安装用于管理用户和项目的 Gitosis

```shell
$ git clone https://github.com/res0nat0r/gitosis.git && cd gitosis
$ sudo python setup.py install
```

<!--more-->

新建一个单独用于 git 的账户，`-m` 用于生成用户文件夹

```shell
$ useradd -m git
$ passwd git
```



对 Gitosis 进行配置

1. 在 **本机** 生成公钥

   ```shell
   $ ssh-keygen -t rsa
   ```

2. 上传公钥至服务器并激活Gitosis，可以使用记事本配合 vi 复制到服务器端

   ```shell
   $ su git
   ```

3. 初始化Gitosis

   ```shell
   $ gitosis-init < /home/git/id_rsa.pub
   ```

到这里 Git 服务器就好了

* 默认的仓库地址是 /home/git/repositories
* Git 管理员用户是 git



配置用户

1. 在本机，将 gitosis-admin 仓库克隆下来

```shell
$ git clone git@192.168.0.10:gitosis-admin.git
```

2. 修改 **keydir** 文件夹和 **gitosis.conf**

   **keydir** 文件夹存放公钥 `username.pub` 文件，**gitosis.conf** 中如下填写，意思是用户 username 对 gitosis-admin test 两个仓库具有写的属性（只读属性配置members 和 readonly）

```shell
[group gitosis-admin]
members = username
writable = gitosis-admin test
```

3. 修改后的仓库推送到服务器

```shell
$ git add .
$ git commit -m "add user"
$ git push git@192.168.0.10:gitosis-admin.git master
```



------



到这里已经配置成功了，本地项目 test 推送流程

```shell
$ cd test
$ git init
$ git add .
$ git commit -m "first commit"
$ git push git@192.168.0.10:test.git master
```

在新项目git-test里首次推送数据到服务器前，需先设定该服务器地址为远程仓库，但你不用事先到服务器上手工创建该项目的裸仓库— Gitosis 会在第一次遇到推送时自动创建（貌似 gitosis.conf 需要配置权限）。

目前没有找到匿名用户 clone 的方法，只能手工加载所有人的 公钥 文件。

生成公钥：

```powershell
PS ssh-keygen -t rsa
```

路径 C:\Users\你的用户名\.ssh