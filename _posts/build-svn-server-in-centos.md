---
title: Unix下搭建SVN服务器(CentOS)
date: 2016-09-23 23:59:47
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/svn-thumbnail.jpg
tags: 
	- SVN
	- 版本控制
categories: 
	- 架构师
	- Linux
keywords:
	- SVN
	- 版本控制
---
说起SVN，每个人或多或少都会接触过一些，目前主流的两种版本控制工具就是SVN和Git，在Git诞生之前，整个天下就是SVN的。即使是现在，仍然有相当的一部分企业采用SVN进行版本的控制。说到这里，有的人可能会问到，为什么要使用版本控制呢？这个问题啊，最简单地回答就是可以在需要的时候快速回溯到之前的版本，保护自己所做的工作，复杂的定义请自行百度。

## 安装SVN
在CentOS里面，安装软件非常的方便，使用`yum install *`的命令就可以了，所以我们使用`yum install subversion`来安装这个版本控制工具。

``` bash
[root@richard Documents]# yum install subversion
Loading mirror speeds from cached hostfile
 * base: ftp.sjtu.edu.cn
 * extras: mirrors.cn99.com
 * updates: ftp.sjtu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package subversion.x86_64 0:1.6.11-15.el6_7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

========================================================================================================================
 Package                      Arch                     Version                             Repository              Size
========================================================================================================================
Installing:
 subversion                   x86_64                   1.6.11-15.el6_7                     base                   2.3 M

Transaction Summary
========================================================================================================================
Install       1 Package(s)

Total download size: 2.3 M
Installed size: 12 M
Is this ok [y/N]: y
Downloading Packages:
subversion-1.6.11-15.el6_7.x86_64.rpm                                                            | 2.3 MB     00:16
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : subversion-1.6.11-15.el6_7.x86_64                                                                    1/1
  Verifying  : subversion-1.6.11-15.el6_7.x86_64                                                                    1/1

Installed:
  subversion.x86_64 0:1.6.11-15.el6_7

Complete!
```

安装完毕可以使用`svn --version`的命令来查看版本，以确保安装成功。

``` bash
[root@richard Documents]# svn --version
svn, version 1.6.11 (r934486)
   compiled Aug 17 2015, 08:37:43

Copyright (C) 2000-2009 CollabNet.
Subversion is open source software, see http://subversion.tigris.org/
This product includes software developed by CollabNet (http://www.Collab.Net/).

The following repository access (RA) modules are available:

* ra_neon : Module for accessing a repository via WebDAV protocol using Neon.
  - handles 'http' scheme
  - handles 'https' scheme
* ra_svn : Module for accessing a repository using the svn network protocol.
  - with Cyrus SASL authentication
  - handles 'svn' scheme
* ra_local : Module for accessing a repository on local disk.
  - handles 'file' scheme

```
## 配置SVN服务端

这里需要使用svnadmin的命令，我们先创建一个SVN的服务包存放地址。

``` bash
[root@richard Documents]# mkdir -pv repo
mkdir: created directory `repo'
[root@richard Documents]# ls
repo
[root@richard Documents]# cd repo/
[root@richard repo]# pwd
/root/Documents/repo
[root@richard repo]# svnadmin create /root/Documents/repo/
[root@richard repo]# ls
conf  db  format  hooks  locks  README.txt 
```

配置conf目录下的三个文件

authz

``` conf
# harry = rw
# &joe = r
# * =

# [repository:/baz/fuz]
# @harry_and_sally = rw*
# = r
admin = richard

[/]
richard = rw
@admin = rw

[/svn]
richard = rw
@admin = rw
* =

[/documents]
richard = rw
@admin = rw
* =

[/code]
richard = rw
@admin = rw
* =      
```

passwd

``` conf
### This file is an example password file for svnserve.
### Its format is similar to that of svnserve.conf. As shown in the
### example below it contains one section labelled [users].
### The name and password for each user follow, one account per line.

[users]
# harry = harryssecret
# sally = sallyssecret
richard = 123456
```

svnserver.conf

``` conf
### These options control access to the repository for unauthenticated
### and authenticated users.  Valid values are "write", "read",
### and "none".  The sample settings below are the defaults.
anon-access = read
auth-access = write
### The password-db option controls the location of the password
### database file.  Unless you specify a path starting with a /,
### the file's location is relative to the directory containing
### this configuration file.
### If SASL is enabled (see below), this file will NOT be used.
### Uncomment the line below to use the default password file.
password-db = passwd
### The authz-db option controls the location of the authorization
### rules for path-based access control.  Unless you specify a path
### starting with a /, the file's location is relative to the the
### directory containing this file.  If you don't specify an
### authz-db, no path-based access control is done.
### Uncomment the line below to use the default authorization file.
authz-db = authz
### This option specifies the authentication realm of the repository.
### If two repositories have the same authentication realm, they should
### have the same password database, and vice versa.  The default realm
### is repository's uuid.
# realm = /root/Documents/repo/
```

启动SVN服务器：

``` bash
svnserve -d -r /root/Documents/repo/
```

## 客户端连接测试

客户端有很多形式，最常用的几种客户端分别是TortoiseSVN和SmartSVN，不过博主在这儿想介绍的是SVN命令的客户端。

``` bash
svn checkout svn://*.*.*.* . #这个点表示在当前目录下检出
```

`注意` 博主注意到，如果是连不上失败的话，有两种原因，第一种，SVN服务器没启动，第二种，服务器的防火墙策略。你需要修改防火墙的设置，这里博主给出一份iptables的清单，供大家参考。

``` bash
[root@richard conf]# more /etc/sysconfig/iptables
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8081 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8009 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3690 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT   
```

如果自己想添加新的端口，直接按照上面的格式就OK了。