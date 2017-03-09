---
title: CentOS7 SSH免登录配置
date: 2017-02-19 12:46:37
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/centos72.jpg
tags:
	- Hadoop
	- Linux
categories:
	- 架构师
	- Linux
keywords:
	- Hadoop
	- Linux
---
之前写过一篇关于SSH免登录的文章，不过那个是专门针对hadoop来设置的，感觉写的不是很详细，所以重新写一篇操作更具体的文章。供大家参考参考。本文的操作环境是CentOS7系统，一共有四台服务器，分别被命名为master，slave1， slave2以及slave3。

## 设置hostname
在CentOS7中，hosts文件的所在地是`/etc/hosts`里面，大家直接使用vim工具将这个hosts文件打开，配置方式同windows或者mac一样。下面给出一个范例：

``` bash
127.0.0.1  localhost localhost.localdomain localhost4 localhost4.localdomain4
::1        localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.131.140 master
192.168.131.141 slave1
192.168.131.142 slave2
192.168.131.143 slave3
```

大家可以根据自己的实际情况进行相应地修改。

## 配置SSH免登录
使用一个工具`ssh-keygen`在每一台机器上生成对应的私钥和公钥。命令为`ssh-keygen -t rsa -P ""`，然后一路回车就行：

``` bash
ssh-keygen -t rsa -P ""
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
a0:0d:8a:b4:dd:e7:92:6f:44:25:36:9a:5b:75:10:a8 root@master
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
按照同样的命令，在三台从服务器上分别生成对应的公钥和私钥。接下来就是将这些公钥拷贝到主服务器上来。

``` bash
scp ~/.ssh/id_rsa.pub root@master:~/.ssh/id_rsa.pub.slave1
scp ~/.ssh/id_rsa.pub root@master:~/.ssh/id_rsa.pub.slave2
scp ~/.ssh/id_rsa.pub root@master:~/.ssh/id_rsa.pub.slave3
```
接着将这些拷贝过来的公钥保存在主服务器的公钥文件`~/.ssh/authorized_keys`中。

``` bash
cat ~/.ssh/id_rsa.pub* >> ~/.ssh/authorized_keys
```
然后将这个公钥文件分发给每一个从服务器。

``` bash
scp ~/.ssh/authorized_keys root@slave1:~/.ssh/
scp ~/.ssh/authorized_keys root@slave2:~/.ssh/
scp ~/.ssh/authorized_keys root@slave3:~/.ssh/
```

好了。

## 测试SSH免登录
在每一台主机上面分别使用ssh命令来登录其他三台机器，看能否成功？

``` bash
[root@slave1 ~]# ssh master
Last login: Sun Feb 19 13:08:38 2017 from slave2
[root@master ~]# logout
Connection to master closed. 
```

成功！