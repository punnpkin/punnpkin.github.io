---
layout:     post
title:      记录 vsftpd 的配置
category:   Linux
tags:       
---

记录 vsftpd 的配置

ubuntu 上的 ftp 可以说是配置了一万遍了，最初学 Linux 命令都没学全的时候就开始配置 vsftpd 了。但是，泪奔的是每次配置都要弄半天，心中好似一万头草泥马跑过 (╯‵□′)╯︵┻━┻

这次一定详详细细的记录一下

vsftpd 是 very secure FTP daemon 的缩写（非常安全的 FTP 守护程序）

**安装：**

```shell
$ sudo apt install vsftpd
```

<!--more-->

**配置 `/etc/vsftpd.conf`：**

```shell
# 默认配置不修改
listen=NO
listen_ipv6=YES
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO
seccomp_sandbox=NO

# 匿名访问 
anonymous_enable=NO
# 本机用户访问
local_enable=YES
# 是否可以写入
write_enable=YES
# 新建文件的权限 777-022=755
local_umask=022

# 启用 userlist 模式
userlist_deny=NO
userlist_enable=YES
userlist_file=/etc/allowed_users

# 限制本地用户切换根目录的权限 YES 开启限制
chroot_local_user=YES
# 切换根目录权限例外用户，没有则不用开启
# chroot_list_file=/etc/vsftpd.chroot_list
# 如果用户限定在主目录下，则需要开启写入权限
allow_writeable_chroot=YES
```

**创建FTP目录：**

```shell
$ mkdir /home/ftp
```

**修改权限：**

```shell
$ chmod -R 777 /home/ftp
```

**添加专用用户：**

```shell
# -d 指定用户登入的起始目录 -s 指定用户登录所使用的 Shell
$ sudo useradd -d /home/ftp -s /sbin/nologin ftp
# 修改密码
$ sudo passwd ftp
```

在 `allowed_users` 中添加用户，写入用户名，多个用户需要换行。

**修改 `/etc/pam_shells.so`:**

`auth required pam_shells.so` 配置项的含义为 仅允许用户的shell为 `/etc/shells` 文件内的shell命令时，才能够成功，而创建 ftp 用户时使用了 `/sbin/nologin` ，这个 shell 不在 `/etc/shells` 里面。

注释掉

```shell
#auth    required pam_shells.so
```

**重启服务**

```shell
$ sudo service vsftpd restart
$ sudo service vsftpd status
```

