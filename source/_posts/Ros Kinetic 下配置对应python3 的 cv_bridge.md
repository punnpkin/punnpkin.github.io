---
layout:     post
title:      Ros Kinetic 下配置对应 python3 的 cv_bridge
category:   Linux
---

### 0. 前言

> DEPRECATION: Python 2.7 will reach the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 won't be maintained after that date.

`python 2.7` 即将在今年停止支持，但很多基于 `Ros Kinetic` 的包都是基于 `python 2.7` 的，在 Ubuntu 不能升级的情况下也不能安装更高版本的 `Ros`，由于最近用到的一些包是在 `python 3` 下的, 不得不考虑一下在 `Ros Kinetic` 下构建包，当然前后花费了好长时间，困难重重，几度奔溃。

<!--more-->

### 1. 构建 python 3 虚拟环境

看 [这里](https://youngnan.tk/2019/04/07/virtualenv/)

使用虚拟环境的原因是需要用到环境里搭好的 `Tensorflow` 库，当然不使用虚拟环境也没关系。

### 2. 移除 ros 自带的 cv.so 文件

```shell
/opt/ros/kinetic/lib/python2.7/dist-packages$ sudo mv cv2.so cv2_ros.so
# 很重要
```

### 3. 构建针对 python3 的 boost 库

```shell
# Boost是一个功能强大、构造精巧、跨平台、开源并且完全免费的C++程序库 
# 并不知道是干嘛的
$ sudo update-alternatives --config python

$ ./bootstrap.sh --with-python-version=PYTHON

$ ./b2

$ sudo ./b2 install
```

### 4. 构建针对 python3 的 cv_bridge 库

```shell
$ sudo apt-get install python-catkin-tools python3-dev python3-catkin-pkg-modules \
                       python3-numpy python3-yaml ros-kinetic-cv-bridge

# 新建一个工作区 
# 非常重要 在原有的工作区构建会冲突
$ echo $ROS_PACKAGE_PATH

# 多工作区的问题可能存在包名冲突
# 使用 $ unset $ROS_PACKAGE_PATH 清理工作区

$ git clone https://github.com/ros-perception/vision_opencv.git
$ cd src/vision_opencv/

$ git checkout 1.12.8
$ mkdir build

# 切换 python3
$ sudo update-alternatives --config python

# 非常重要 建立软连接
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py35.so /usr/lib/x86_64-linux-gnu/libboost_python3.so

$ cd build
$ cmake ..
$ make
$ sudo make install

# 切换回 python 2
$ sudo update-alternatives --config python
```

### 5. 验证

```shell
# 先启用虚拟环境，再加入catkin环境，不要在同一个终端运行
# source 环境

$ source venv/bin/activate
$ source cv_bridge_ws/devel/setup.bash
$ python3
Python 3.5.2 (default, Oct  8 2019, 13:06:37) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from cv_bridge.boost.cv_bridge_boost import getCvType
>>>

# ps 真是痛苦啊，学艺不精，中途把 usr/lib/ 下的库都干掉了，桌面都坏了，
# 折腾了几天终于合适了 前路漫漫啊
```
