---
layout:     post
title:      记录 nodejs 的安装过程
category:   Linux
tags:       
---

记录 nodejs 的安装过程

直接用 apt 安装的 nodejs 不是最新版

需要到官网下载：

```shell
$ wget https://npm.taobao.org/mirrors/node/v10.15.3/node-v10.15.3-linux-x64.tar.xz
```

解压 `.xz` 文件使用：

```shell
$ tar -xvf ***.tar.xz
```

`tar` 命令是一个参数很多的命令

<!--more-->

```shell
# 打包
$ tar -cvf log.tar log2012.log       #仅打包，不压缩！ 
$ tar -zcvf log.tar.gz log2012.log   #打包后，以 gzip 压缩 
$ tar -zcvf log.tar.bz2 log2012.log  #打包后，以 bzip2 压缩
# 在参数 f 之后的文件档名是自己取的，习惯上都用 .tar 来作为辨识。 如果加 z 参数，则以 .tar.gz 或 .tgz 来代表 gzip 压缩过的 tar 包； 如果加 j 参数，则以 .tar.bz2 来作为 tar 包名

# 查阅
$ tar -ztvf log.tar.gz
# 使用 gzip 压缩的log.tar.gz，所以要查阅log.tar.gz包内的文件时，就得要加上 z 这个参数

# 解压
$ tar -zxvf /opt/soft/test/log.tar.gz

```

把当前的文件解压到 `/opt` 目录下，然后编辑 `/etc/profile` 文件：

```shell
#SET PATH FOR NODEJS
export NODE_HOME=/opt/node-v10.15.3-linux-x64
export PATH=$NODE_HOME/bin:$PATH
```

然后使用 `source /etc/profile` 使配置生效。

使用：

```shell
$ node -v
$ npm -v
```

查看版本号