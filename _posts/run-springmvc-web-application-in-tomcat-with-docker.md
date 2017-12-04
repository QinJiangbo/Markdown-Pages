---
title: 利用Docker运行SpringMVC程序
date: 2017-10-13 21:12:55
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/docker-logo.jpeg
tags:
	- docker
	- tomcat
	- springmvc
categories:
	- 架构师
	- Docker
keywords:
	- docker
	- tomcat
	- springmvc
---
![Docker](https://obrxbqjbi.qnssl.com/blog/image/docker-logo.jpeg)

本文主要介绍如何利用Docker来运行一个Web应用程序。因为博主是以Java为主，所以来讲一讲我们最熟悉的SpringMVC应用程序是如何在Docker中跑起来的。首先要检查一下我们需要的东西：

+ Docker ([https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac))
+ Tomcat ([http://www-eu.apache.org/dist/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.tar.gz](http://www-eu.apache.org/dist/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.tar.gz))
+ JDK ([http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz](http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz))

这三大件就是我们整个应用能启动成功运行的基础。

## 安装环境
我们需要为这个应用建立一个镜像，首先创建一个目录用于存放Dockerfile以及Tomcat和JDK等环境。

``` sh
$ sudo mkdir centos_tomcat
```
进入`centos_tomcat`目录，下载我们必须的一些环境（Docker的安装就不演示了，默认看这篇文章的你已经安装好了Docker~）：

``` sh
$ cd centos_tomcat
$ wget http://www-eu.apache.org/dist/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.tar.gz
$ wget http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz
$ tar -zxvf apache-tomcat-8.5.23.tar.gz
$ tar -zxvf jdk-8u144-linux-x64.tar.gz
$ ls
apache-tomcat-8.5.23 jdk1.8.0_144
```
好了，目前这两个环境已经准备好了，我们现在需要准备一下Dockerfile和启动脚本。

## 编写启动脚本
将启动脚本的命名定为`run.sh`，（貌似大家都在用这个名字，如果你觉得不对，可以随便改哈~），脚本如下：

``` sh
#!/bin/bash
/usr/sbin/sshd -D &
exec ${CATALINA_HOME}/bin/catalina.sh run
```
注意到上面脚本中第一行为`/usr/sbin/sshd -D &`，这是因为我们将需要这个容器提供SSH连接服务，也就是说，我们会继承上一篇博客实现的SSH镜像。这里的`&`符号表示以后台服务启动。

## 编写Dockerfile
关于SpringMVC项目如何的创建以及打包不在本文的讨论范围之内，如果你不明白的话可以查找相关的文章参考一下。我们已经创建好了一个应用叫`SpringDocker`，使用Maven进行构建，打成了一个war包。

我们的基础镜像是`sshd_centos`，脚本如下：

``` sh
# 指定基础镜像
FROM sshd_centos

# 维护者信息
MAINTAINER qinjiangbo<qinjiangbo1994@163.com>

# 设置环境变量
ENV CATALINA_HOME /tomcat
ENV JAVA_HOME /jdk

# 复制tomcat和jdk到镜像中
ADD apache-tomcat-8.5.23 /tomcat
ADD jdk1.8.0_144 /jdk

# 加入应用到镜像中
ADD SpringDocker.war /tomcat/webapps

# 添加启动脚本
ADD run.sh /usr/local/sbin/run.sh
RUN chmod 755 /usr/local/sbin/run.sh
RUN chmod 755 /tomcat/bin/*.sh

# 开放端口
EXPOSE 8080

# 容器启动执行脚本
CMD ["/usr/local/sbin/run.sh"]
```

脚本写好了以后需要执行构建命令：

``` sh
$ docker build -t tomcat_centos .     
Sending build context to Docker daemon  396.5MB
Step 1/12 : FROM sshd_centos
 ---> f0560ea85754
Step 2/12 : MAINTAINER qinjiangbo<qinjiangbo1994@163.com>
 ---> Running in 3d71c2c9cf98
 ---> f0460a80934d
Removing intermediate container 3d71c2c9cf98
Step 3/12 : ENV CATALINA_HOME /tomcat
 ---> Running in f5c8cd1f054d
 ---> 39e81d6b6091
Removing intermediate container f5c8cd1f054d
Step 4/12 : ENV JAVA_HOME /jdk
 ---> Running in 4b4b8e85e787
 ---> 5573f336885e
Removing intermediate container 4b4b8e85e787
Step 5/12 : ADD apache-tomcat-8.5.23 /tomcat
 ---> fb62c6291978
Step 6/12 : ADD jdk1.8.0_144 /jdk
 ---> 19b628cc8a47
Step 7/12 : ADD SpringDocker.war /tomcat/webapps
 ---> e635cb335ec9
Step 8/12 : ADD run.sh /usr/local/sbin/run.sh
 ---> 632faa035eea
Step 9/12 : RUN chmod 755 /usr/local/sbin/run.sh
 ---> Running in ce1d588035eb
 ---> bf74faf09c94
Removing intermediate container ce1d588035eb
Step 10/12 : RUN chmod 755 /tomcat/bin/*.sh
 ---> Running in e80f74764da3
 ---> cc700674e693
Removing intermediate container e80f74764da3
Step 11/12 : EXPOSE 8080
 ---> Running in 8f472a55cb02
 ---> 954525411f30
Removing intermediate container 8f472a55cb02
Step 12/12 : CMD /usr/local/sbin/run.sh
 ---> Running in 81ed01144c89
 ---> 6712f0870a5b
Removing intermediate container 81ed01144c89
Successfully built 6712f0870a5b
Successfully tagged tomcat_centos:latest
```
注意到构建命令的最后还有一个**`.`**哈，表示当前目录，否则的话需要指定全路径的。使用`docker images`来查看我们已有的镜像：

``` sh
$ docker images                                    
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat_centos       latest              6712f0870a5b        29 seconds ago      615MB
sshd_centos         latest              f0560ea85754        2 days ago          221MB
nginx               latest              1e5ab59102ce        4 days ago          108MB
gitlab/gitlab-ce    latest              453d64ae84c7        8 days ago          1.28GB
centos              latest              196e0ce0c9fb        4 weeks ago         197MB
```
可以看到镜像已经构建完成。接下来就是运行我们的SpringMVC应用。

## 运行应用
查看当前目录：

``` sh
$ ls
Dockerfile           apache-tomcat-8.5.23 run.sh
SpringDocker.war     jdk1.8.0_144
```

启动容器来运行应用：

``` sh
$ docker run -d -p 8080:8080 -p 22:22 tomcat_centos
203eafc6d2e0935ca77690a6decf1edc772281354f86295dd82028ca50c461a5
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                                        NAMES
203eafc6d2e0        tomcat_centos       "/usr/local/sbin/r..."   6 seconds ago       Up 8 seconds                0.0.0.0:22->22/tcp, 0.0.0.0:8080->8080/tcp   flamboyant_carson
```
输入`http://localhost:8080/SpringDocker/`查看一下页面效果：
![应用截图](https://obrxbqjbi.qnssl.com/blog/image/springmvc-docker-app.png)
运行成功！