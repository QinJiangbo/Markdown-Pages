---
title: 使用命令chown改变目录或文件的所有权
date: 2016-08-18 00:57:23
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/linux-2-thumbnail.jpg
tags:
	- Linux
	- Chown
categories:
	- 架构师
	- Linux
keywords:
	- Linux
	- Chown
---
![](https://obrxbqjbi.qnssl.com/blog/image/linux-2-thumbnail.jpg)

文件与目录不仅可以改变权限，其所有权及所属用户组也能修改，和设置权限类似，用户可以通过图形界面来设置，或执行chown命令来修改。

我们先执行ls -l看看目录情况：

``` bash
[root@localhost ~]# ls -l
总用量 368
-rwxrwxrwx 1 root root 12172 8月 15 23:18 conkyrc.sample
drwxr-xr-x 2 root root 48 9月 4 16:32 Desktop
-r--r--r-- 1 root root 331844 10月 22 21:08 libfreetype.so.6
drwxr-xr-x 2 root root 48 8月 12 22:25 MyMusic
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth0
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth1
-rwxr-xr-x 1 root root 512 11月 5 08:08 net.lo
drwxr-xr-x 2 root root 48 9月 6 13:06 vmware
```

可以看到conkyrc.sample文件的所属用户组为root，所有者为root。

执行下面命令，把conkyrc.sample文件的所有权转移到用户user:

``` bash
[root@localhost ~]# chown user conkyrc.sample
[root@localhost ~]# ls -l
总用量 368
-rwxrwxrwx 1 user root 12172 8月 15 23:18 conkyrc.sample
drwxr-xr-x 2 root root 48 9月 4 16:32 Desktop
-r--r--r-- 1 root root 331844 10月 22 21:08 libfreetype.so.6
drwxr-xr-x 2 root root 48 8月 12 22:25 MyMusic
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth0
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth1
-rwxr-xr-x 1 root root 512 11月 5 08:08 net.lo
drwxr-xr-x 2 root root 48 9月 6 13:06 vmware
```

要改变所属组，可使用下面命令：

``` bash
[root@localhost ~]# chown :users conkyrc.sample
[root@localhost ~]# ls -l
总用量 368
-rwxrwxrwx 1 user users 12172 8月 15 23:18 conkyrc.sample
drwxr-xr-x 2 root root 48 9月 4 16:32 Desktop
-r--r--r-- 1 root root 331844 10月 22 21:08 libfreetype.so.6
drwxr-xr-x 2 root root 48 8月 12 22:25 MyMusic
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth0
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth1
-rwxr-xr-x 1 root root 512 11月 5 08:08 net.lo
drwxr-xr-x 2 root root 48 9月 6 13:06 vmware
```

要修改目录的权限，使用－R参数就可以了，方法和前面一样