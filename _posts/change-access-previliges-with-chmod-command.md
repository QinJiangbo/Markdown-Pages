---
title: 使用chmod改变文件或目录的访问权限
date: 2016-08-18 00:50:36
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/linux-2-thumbnail.jpg
tags:
	- Linux
	- Chmod
categories:
	- 架构师
	- Linux
keywords:
	- Linux
	- Chmod
---
![](https://obrxbqjbi.qnssl.com/blog/image/linux-2-thumbnail.jpg)

文件和目录的权限表示，是用rwx这三个字符来代表所有者、用户组和其他用户的权限。有时候，字符似乎过于麻烦，因此还有另外一种方法是以数字来表示权限，而且仅需三个数字。

- r: 对应数值4
- w: 对应数值2
- x：对应数值1
- －：对应数值0

数字设定的关键是mode的取值，一开始许多初学者会被搞糊涂，其实很简单，我们将rwx看成二进制数，如果有则有1表示，没有则有0表示，那么rwx r-x r- -则可以表示成为：

111 101 100

再将其每三位转换成为一个十进制数，就是754。

例如，我们想让a.txt这个文件的权限为：

自己同组用户其他用户

- 可读是是是
- 可写是是
- 可执行

那么，我们先根据上表得到权限串为：rw-rw-r--，那么转换成二进制数就是110 110 100，再每三位转换成为一个十进制数，就得到664，因此我们执行命令：

[root@localhost ~]# chmod 664 a.txt

按照上面的规则，rwx合起来就是4 2 1＝7，一个rwxrwxrwx权限全开放的文件，数值表示为777；而完全不开放权限的文件“－－－－－－－－－”其数字表示为000。下面举几个例子：

- -rwx------:等于数字表示700。
- -rwxr—r--:等于数字表示744。
- -rw-rw-r-x:等于数字表示665。
- drwx—x—x:等于数字表示711。
- drwx------:等于数字表示700。

在文本模式下，可执行chmod命令去改变文件和目录的权限。我们先执行ls -l 看看目录内的情况：

``` bash
[root@localhost ~]# ls -l
总用量 368
-rw-r--r-- 1 root root 12172 8月 15 23:18 conkyrc.sample
drwxr-xr-x 2 root root 48 9月 4 16:32 Desktop
-r--r--r-- 1 root root 331844 10月 22 21:08 libfreetype.so.6
drwxr-xr-x 2 root root 48 8月 12 22:25 MyMusic
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth0
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth1
-rwxr-xr-x 1 root root 512 11月 5 08:08 net.lo
drwxr-xr-x 2 root root 48 9月 6 13:06 vmware
```

可以看到当然文件conkyrc.sample文件的权限是644,然后把这个文件的权限改成777。执行下面命令

[root@localhost ~]# chmod 777 conkyrc.sample

然后ls -l看一下执行后的结果：

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

可以看到conkyrc.sample文件的权限已经修改为rwxrwxrwx

如果要加上特殊权限，就必须使用4位数字才能表示。特殊权限的对应数值为：

- s或 S （SUID）：对应数值4。
- s或 S （SGID）：对应数值2。
- t或 T ：对应数值1。

用同样的方法修改文件权限就可以了, 例如：

``` bash
[root@localhost ~]# chmod 7600 conkyrc.sample
[root@localhost ~]# ls -l
总用量 368
-rwS--S--T 1 root root 12172 8月 15 23:18 conkyrc.sample
drwxr-xr-x 2 root root 48 9月 4 16:32 Desktop
-r--r--r-- 1 root root 331844 10月 22 21:08 libfreetype.so.6
drwxr-xr-x 2 root root 48 8月 12 22:25 MyMusic
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth0
-rwxr-xr-x 1 root root 9776 11月 5 08:08 net.eth1
-rwxr-xr-x 1 root root 512 11月 5 08:08 net.lo
drwxr-xr-x 2 root root 48 9月 6 13:06 vmware
```

加入想一次修改某个目录下所有文件的权限，包括子目录中的文件权限也要修改，要使用参数－R表示启动递归处理。

例如：

[root@localhost ~]# chmod 777 /home/user 注：仅把/home/user目录的权限设置为rwxrwxrwx

[root@localhost ~]# chmod -R 777 /home/user 注：表示将整个/home/user目录与其中的文件和子目录的权限都设置为rwxrwxrwx