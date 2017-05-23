---
title: Linux下使用Nexus搭建Maven私服
date: 2017-05-11 00:27:31
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/maven-thumbnail.png
tags:
	- maven
	- 私服
	- nexus
categories:
	- 架构师
	- Linux
keywords:
	- maven
	- nexus
---
`说明`：这是我去年写的一篇文章，一直在印象笔记里存着，这几天整理的时候发现了，觉得还是有不少参考价值的，故重新整理一下发表出来。由于当时直接截的图，所以本文中图片比较多，使用手机流量者慎入，土豪随意。

## 正文
使用Maven也有一段时间了，现在总结几个比较需要注意的地方，以便以后可以更快搭建一个私服。我的当前版本是nexus-2.13.0-01,所以以这个来说明。

### （1）设置私服的端口和根路径名称
进入目录nexus-2.13.0-01的conf目录，通过vim打开nexus.properties文件如下：

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-01.png)
可以看到application-port字段和nexus-webapp-context-path字段，修改它们为所需字段，保存退出。

### （2）修改Central的Remote Indexes策略
登录主页，默认用户名和密码是admin/admin123
看到如下信息，将Download remote indexes设置为True

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-02.png)

### （3）添加自定义的用户名和密码
进入左侧菜单Security下的Users子菜单，添加一个用户，这个用户是你自己使用的，拥有超级权限。

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-03.png)
然后使用这个账号登录系统，并将默认的admin账号删除。同时将central移到ordered repositories。

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-04.png)

### （4）Maven配置---settings文件设置
首先配置两个服务器server

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-05.png)
其中用户名和密码就是前面说的自定义的用户名和密码，id可以随便填，但是后面在pom中会用到。

### （5）Maven配置---mirrors镜像配置
mirrors必须要配置，因为如果不配置的话maven很有可能会直接绕过私服去中心库下载，并且不会将jar包丢到私服，这样我们的私服就没起到作用。所以必须添加一个镜像管理，值得所有的下载都得通过镜像也就是我们的私服。

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-06.png)

### （6）Maven配置---profiles配置
定义jar库和plugin库的位置，同时还需要确定jdk版本，我的定义的是1.8

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-07.png)
![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-08.png)
 别忘了最后有一个激活的操作。
 
### （7）Maven配置---pom配置
![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-09.png)
这个其实是Maven项目中用来向私服提交jar的一个配置，主要是在项目的deploy操作中起作用。

### （8）Gradle配置---build.gradle配置
a）引入以下插件

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-10.png)
b）定义任务和jar包版本属性
c）发布jar到私服上去

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-11.png)
![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-12.png)
d）确定repository库位置

![](https://obrxbqjbi.qnssl.com/blog/image/maven-nexus-13.png)
e）执行gradle tasks查看可用的任务，并且执行gradle install uploadArchives任务将jar上传到私服，否则会报找不到mavenmeta.xml错误。

###  （9）分别对两个项目工具进行测试，如果下载地址是默认的下载地址则说明配置成功。