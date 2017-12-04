---
title: 服务器SSH自动登录脚本
date: 2017-11-11 17:25:20
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/centos-ssh-login.png
tags:
	- SSH
	- 自动登录
	- expect命令
categories:
	- 架构师
	- Linux
keywords:
	- SSH
	- 自动登录
	- expect命令
---
![](https://obrxbqjbi.qnssl.com/blog/image/centos-ssh-login.png)

发现一个有趣的事情，就是很久之前想过的一个问题，不知道解决方案，然后就忘记了。突然某一天（比如今天）又想起来了，于是乎趁这个机会要好好弄明白到底是怎么回事。这个问题就是`Linux系统中如何实现对命令的自动回答`。举个简单的例子哈，比如我们使用`yum install`命令进行安装的时候，如果没有加上`-y`参数，我们几乎很多时候都会在程序安装的过程当中被要求输入`yes/no`，如果不输入程序则停止住了。那么，如何实现自动化的处理呢？对于`yum install`命令而言，`-y`参数是最好的选择，因为简洁。

好了，对于本文所要讲述的脚本来说，是针对于SSH登录的。我们知道，很多时候我们一旦管理的机器多了以后，就需要记住不同的密码，这几乎是一项很大的挑战。所以，我们会使用一些工具，比如说SSH远程登录工具，但这是你可以使用GUI工具的前提。如果在纯CentOS环境下，我们应该如何使用命令去实现命令的自动应答呢？

## expect命令
答案就是`expect`命令，关于`expect`命令的具体使用方式，可以通过命令`man expect`来查看。本文主要介绍`expect`命令下最重要的四个命令：

+ send 向进程发送字符串参数
+ expect 从进程接收字符串参数
+ spawn 启动新的进程
+ interact 允许用户交互

## 实例讲解
针对于CentOS服务器自动登录，我们分析一下登录的流程，（1）通常是我们需要使用`ssh`连接，（2）然后会显示一个提示要求我们输入密码，（3）然后我们就输入密码，回车，（4）就登录成功了！

好了，我们分别来看看怎么实现：

### 使用ssh命令连接
``` sh
spawn ssh root@121.68.209.142
```
启动一个线程连接到服务器`121.68.209.142`。

### 显示一个提示要求输入密码
``` sh
expect "*password:*"
```
注意`expect`命令里面支持通配符。可以抽取实际输出的字符串中的某一段作为判断的依据。

### 输入密码，回车
``` sh
set password pas4word
send "$password\r"
```
这里使用了变量`password`来表示密码，实践的时候建议使用变量，这样便于修改和拓展。

### 人工交互
当前面的操作都成功的时候，我们应该能正常登录啦，所以这个时候要将交接棒交给用户啦，我们需要来操作远程服务器，所以使用如下命令：

``` sh
interact
```
非常简单！

## 实例源码
``` sh
#!/usr/bin/expect -f 

set timeout -1
set password pas4word

spawn ssh root@121.68.209.142
expect "*password:*"
send "$password\r"

interact
```

注意shell脚本中的头是`#!/usr/bin/expect -f `表示我们使用的是`expect`命令工具，所以这个脚本启动的时候需要通过`expect`命令。拷贝这个代码，另存为`aliyun.sh`：

``` sh
expect aliyun.sh
```
就可以自动登录啦：

``` sh
# expect aliyun.sh 
spawn ssh root@121.68.209.142
root@121.68.209.142's password: 
Last failed login: Sat Nov 11 16:46:41 CST 2017 from 113.22.113.82 on ssh:notty
There were 7 failed login attempts since the last successful login.
Last login: Fri Nov 10 23:49:25 2017 from 211.191.151.221

Welcome to Alibaba Cloud Elastic Compute Service !

[root@richard ~]# 
```

## 总结
遇到问题还是要及时解决，问题总是这样的，如果某个时刻不解决，那你就会一直不清楚。所以唯一的方式就是主动地求知，去查找相关的资料，不一定要记住，但是要知道如何去利用它解决对应的问题。以`expect`命令来说，它的主要使命就是解决命令行里面的自动应答问题。所以，当你有相关的需求时，可以使用`expect`命令来解决。