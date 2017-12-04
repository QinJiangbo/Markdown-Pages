---
title: macOS命令行SSH中文显示
date: 2017-11-27 15:00:41
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/shell-window.png
tags:
	- macOS
	- ssh
	- 中文乱码
categories:
	- 架构师
	- Linux
keywords:
	- macOS
	- ssh
	- 中文乱码
---
![](https://obrxbqjbi.qnssl.com/blog/image/shell-window.png)

使用macOS开发的同学对于终端的依赖程度应该是非常高的，虽然我们有ZOC，有iTerm等等工具，但是自家的能够用当然是希望使用自家的产品了。关于如何使用macOS系统自带的terminal应用连接远程Linux服务器我就不在这里介绍了，有需要的读者可以查询`ssh`命令是如何使用的。

那么本文其实就是做了一个记录，关于中文字符在macOS终端里面显示乱码的问题，这里其实解决方案非常简单，找到`.bash_profile`或者是`.zshrc`文件，一般在`~/`这个目录下。好的看看命令行操作，这里以`.zshrc`为例：

``` sh
$ cd ~
$ vim .zshrc 
```
按住`shift+G`到达最底部，添加这两行代码即可：

``` sh
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

注意需要`source .zshrc`来使配置生效。这样，我们重新打开一个终端就可以看到正确地显示中文了。

``` sh
Last login: Mon Nov 27 15:10:15 2017 from 212.174.113.171

Welcome to Alibaba Cloud Elastic Compute Service !

[root@richard ~]# ll
total 16580
-rwxr-xr-x 1 root root      181 Nov 11 01:38 deploy_tomcat.sh
-rw-r--r-- 1 root root       81 Nov 21 23:10 README.md
-rw-r--r-- 1 root root 16958311 Nov 27 11:53 ROOT.war
-rwxr-xr-x 1 root root       46 Nov 10 23:50 start_tomcat.sh
-rwxr-xr-x 1 root root       47 Nov 10 23:51 stop_tomcat.sh
-rw-r--r-- 1 root root        0 Nov 27 15:09 中文文本测试.md
[root@richard ~]# 
```

我们可以看到`中文文本测试.md`这个文件正常显示了。