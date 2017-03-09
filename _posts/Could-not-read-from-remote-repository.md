---
title: Could not read from remote repository
date: 2016-08-20 12:28:06
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/github-social-coding.jpg
tags:
	- Git
categories:
	- 架构师
keywords:
	- Git
---
![](https://obrxbqjbi.qnssl.com/blog/image/github-social-coding.jpg)

# 问题描述

``` bash
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

# 问题分析
其实问题描述中已经非常清楚了，Permission denied (publickey)。就是公钥权限问题，一般git不管是上传还是更新文件都需要验证这个公钥，如果公钥不存在或者公钥的权限不够，都会导致更新或者上传失败。可以登录github检查SSH keys的权限。

# 问题解决
需要生成一个公钥，并将其添加到github的SSH keys里面，注意，不是项目的SSH keys，是整个账号的SSH keys。

终端代码：

``` bash
ssh-keygen -t rsa -C "qinjiangbo"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Richard/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/Richard/.ssh/id_rsa.
Your public key has been saved in /Users/Richard/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx qinjiangbo
The key's randomart image is:
+---[RSA 2048]----+
| ..    .oB==.    |
|  .o  ..==+.B    |
|  oo.. o+oo= *   |
| . .+ ..+oB E .  |
|  . .o. S* = =   |
|   + . .    *    |
|  . o .    . .   |
|   .             |
|                 |
+----[SHA256]-----+

cat /Users/Richard/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1xxxxxxxxxxxxxxxxxxxxxxxUZDXD qinjiangbo
```

将cat出来的内容拷贝到github账号中，完成！然后在进行pull或者push就OK啦！