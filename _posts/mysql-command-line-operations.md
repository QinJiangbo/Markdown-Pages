---
title: MySQL命令行操作
date: 2016-08-13 02:36:01
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/mysql-thumbnail.png
tags:
	- MySQL
	- 命令行
categories:
	- 数据科学
	- MySQL
keywords:
---
## 一、连接MYSQL。 
**格式**： mysql -h 主机地址 -u 用户名 －p 用户密码 

例1：连接到本机上的MYSQL。 
首先在打开DOS窗口，然后进入目录 mysqlbin，再键入命令`mysql -uroot -p`，回车后提示你输密码，如果刚安装好MYSQL，超级用户`root`是没有密码的，故直接回车即可进入到MYSQL中了，MYSQL的提示符是：`mysql＞` 
<!--more-->

例2：连接到远程主机上的MYSQL。假设远程主机的IP为：`110 
.110.110.110`，用户名为`root`,密码为`abcd123`。则键入以下命令：

``` sql
mysql -h110.110.110.110 -u root -p abcd123
```

（注:u与root可以不用加空格，其它也一样） 

3、退出MySQL命令： `exit` （回车）

## 二、修改密码。 
**格式**：mysqladmin -u 用户名 -p 旧密码 password 新密码 

1、例1：给`root`加个密码`ab12`。首先在DOS下进入目录mysqlbin，然后键入以下命令 (password 里面不要加命令符)

``` sql
mysqladmin -u root password ab12
```

注：因为开始时root没有密码，所以-p 旧密码一项就可以省略了。 

2、例2：再将`root`的密码改为`djg345`。

``` sql
mysqladmin -u root -p ab12 password djg345
```

## 三、增加新用户。（注意：和上面不同，下面的因为是MYSQL环境中的命令，所以后面都带一个分号作为命令结束符） 
**格式**：grant select on 数据库.* to 用户名@登录主机 identified by "密码" 

例1、增加一个用户`test1`密码为`abc`，让他可以在任何主机上登录，并对所有数据库有查询、插入、修改、删除的权限。首先用以`root`用户连入MYSQL，然后键入以下命令：

``` sql
grant select,insert,update,delete on *.* to test1@"%" Identified by "abc";
```

但例1增加的用户是十分危险的，你想如某个人知道test1的密码，那么他就可以在internet上的任何一台电脑上登录你的mysql数据库并对你的数据可以为所欲为了，解决办法见例2。 

例2、增加一个用户`test2`密码为`abc`,让他只可以在`localhost`上登录，并可以对数据库mydb进行查询、插入、修改、删除的操作（localhost指本地主机，即MYSQL数据库所在的那台主机），这样用户即使用知道test2的密码，他也无法从internet上直接访问数据库，只能通过MYSQL主机上的web页来访问了。

``` sql
grant select,insert,update,delete on mydb.* to test2@localhost identified by "abc";
```

如果你不想test2有密码，可以再打一个命令将密码消掉。

``` sql
grant select,insert,update,delete on mydb.* to test2@localhost identified by "";
```