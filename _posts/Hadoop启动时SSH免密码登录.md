---
title: Hadoop启动时SSH免密码登录
date: 2016-08-16 23:00:26
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/hadoop-thumbnail.png
tags:
	- Hadoop
	- Linux
categories:
	- 数据科学
	- Hadoop
keywords:
	- Hadoop
	- Linux
---
现在在学习**大数据**，买了一台云服务器，照着网上的教程安装的。现在在启动**（start-all.sh）**Hadoop的时候老是要求输入密码，后面在真实环境下不可能每一次通信都要求手动输入密码的，所以，免密码很重要：

现在直接上代码：

``` bash
ssh-keygen -t rsa -P ""
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
a0:0d:8a:b4:dd:e7:92:6f:44:25:36:9a:5b:75:10:a8 root@richard
The key's randomart image is:
+--[ RSA 2048]----+
|        .oo      |
|       = o .     |
| .  . * = .      |
|..o..E +         |
|......=.S        |
|     .+.         |
|     o..         |
|      o.         |
|      ..         |
+-----------------+
```

接下来将密钥写入ssh中

``` bash
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
/etc/init.d/ssh reload
```

再次启动hadoop就没有密码输入的要求了。