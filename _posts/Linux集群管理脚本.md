---
title: Linux集群管理脚本
date: 2016-10-22 17:07:11
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/redhat.png
tags:
	- CentOS
	- 集群管理
categories:
	- 架构师
	- Linux
keywords:
	- CentOS
	- 集群管理
---
不知道你是否遇到下面这种经历，当你面对多台机器需要维护的时候，可能会觉得不知所措，一台机器，两台机器这都还好说，但是如果需要管理的是一个集群（超过50台）呢？一台机器一台机器去管理是不太现实的，那样的话累死了。下面分享一个简单的脚本有助于解决这种管理急群中多台服务器的情况。

前提是你首先得设置主机和各台从机之间的SSH免密码登录。如果不知道的可以去看一下我的另一篇博客[设置SSH免登录](http://t.cn/RVploKn)。补充说明的是需要将主机里面的rsa公钥拷贝到各个从机的相同位置里面。这样主机对从机就可以不用密码登录啦。

## 集群管理脚本

``` sh
#!/bin/bash
 if [ "$#" -ne 2 ]; then
     echo "USAGE: $0 [scp|remote] cmd [file]"
     exit -1
 fi

 cmd_type=$1
 slaves_array=("slave1" "slave2" "slave3")

 for ip in ${slaves_array[*]}
 do
     if [ "$cmd_type" = "remote" ]; then
         cmd=$2
         ssh root@$ip "$cmd"
     elif [ "$cmd_type" = "scp" ]; then
         file=$2
         if [ -d $file ]; then
             scp -r $file root@$ip:$file
         else
             scp $file root@$ip:$file
         fi
     fi
 done
 
 echo "Commands Executed!"
```

## 脚本说明
这个里面大家需要更改的是这个slave1,slave2,slave3为对应的从机的地址，或者是写入一个文件直接读取。