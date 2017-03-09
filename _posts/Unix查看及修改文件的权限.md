---
title: Unix查看及修改文件的权限
date: 2016-08-18 01:00:21
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

在终端输入:

ls -l xxx.xxx （xxx.xxx是文件名）

那么就会出现相类似的信息，主要都是这些：

-rw-rw-r--

一共有10位数

其中：最前面那个 - 代表的是类型

中间那三个 rw- 代表的是所有者（user）

然后那三个 rw- 代表的是组群（group）

最后那三个 r-- 代表的是其他人（other）

然后我再解释一下后面那9位数：

r 表示文件可以被读（read）

w 表示文件可以被写（write）

x 表示文件可以被执行（如果它是程序的话）

\- 表示相应的权限还没有被授予

现在该说说修改文件权限了

在终端输入：

chmod o w xxx.xxx

表示给其他人授予写xxx.xxx这个文件的权限

chmod go-rw xxx.xxx

表示删除xxx.xxx中组群和其他人的读和写的权限

其中：

- u 代表所有者（user）
- g 代表所有者所在的组群（group）
- o 代表其他人，但不是u和g （other）
- a 代表全部的人，也就是包括u，g和o
- r 表示文件可以被读（read）
- w 表示文件可以被写（write）
- x 表示文件可以被执行（如果它是程序的话）

其中：rwx也可以用数字来代替

- r ------------4
- w -----------2
- x ------------1
- \- ------------0

行动：

\+ 表示添加权限

\- 表示删除权限

\= 表示使之成为唯一的权限

当大家都明白了上面的东西之后，那么我们常见的以下的一些权限就很容易都明白了：

-rw------- (600) 只有所有者才有读和写的权限

-rw-r--r-- (644) 只有所有者才有读和写的权限，组群和其他人只有读的权限

-rwx------ (700) 只有所有者才有读，写，执行的权限

-rwxr-xr-x (755) 只有所有者才有读，写，执行的权限，组群和其他人只有读和执行的权限

-rwx--x--x (711) 只有所有者才有读，写，执行的权限，组群和其他人只有执行的权限

-rw-rw-rw- (666) 每个人都有读写的权限

-rwxrwxrwx (777) 每个人都有读写和执行的权限
