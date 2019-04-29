---
layout:     post
title:      记录 virtualenv 的使用
category:   Linux
tags:       pip
---

**记录 virtualenv 的使用**

创建 virtual env 环境，这里上来就报错：

```shell
OSError: Command /root/.local/share/letsencrypt/bin/python2.7 - setuptools pkg_resources pip wheel failed with error code 2
```

查到是由于多个版本的 virtualenv 冲突了，解决：

```shell
$ python -V
$ pip -V
$ dpkg -l|grep virtualenv
```

然后使用

```shell
$ sudo apt-get purge python-virtualenv python3-virtualenv virtualenv
```

删除，重新安装：

```shell
$ pip install virtualenv
```

这里有可能又报错 `permission denied` ，查了好多地方，说是要加 `sudo` ，但其实不是这么解决的，正确的安装方法是：

```shell
$ pip install virtualenv --user
```

即为当前的用户安装 `pip` ，原来自己以前用 `pip` 的姿势都是错的 o(╥﹏╥)o，使用 `pip` 千万不要加 `sudo` 啊，真是学习不规范，使用两行泪〒▽〒

开启虚拟环境：

```shell
// 查找解释器位置
$ find / -name python3
// 指定 python3 创建干净环境
$ sudo virtualenv --no-site-packages -p /usr/bin/python3 venv
$ source venv/bin/activate
// 查看版本
$ pip -V
$ python -V
```

然后就可以愉快的使用了。。。