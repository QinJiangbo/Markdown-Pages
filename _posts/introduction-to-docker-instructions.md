---
title: Dockerfile常用命令介绍
date: 2017-10-16 15:30:40
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/docker-logo.jpeg
tags:
	- Docker
	- 命令
categories:
	- 架构师
	- Docker
keywords:
	- Docker
	- 命令
---
![Docker](https://obrxbqjbi.qnssl.com/blog/image/docker-logo.jpeg)

前面几篇文章介绍了Docker如何构建一个镜像以及如何运行一个容器，其实这个是在大家有Docker基础的前提下进行的，对于没有基础的同学，本文简单介绍一下Dockerfile中各个命令的用法是怎样的。

## 1. FROM
格式：`FROM <image>`或者`FROM <image>:<tag>`
第一条指令必须是`FROM`，而且，在同一个Dockerfile中创建多个镜像的时候，可以使用多个`FROM`指令（每个指令一次）。

## 2. MAINTAINER
格式：`MAINTAINER <name>`
指定维护者是谁。

## 3. RUN
格式：`RUN <command>`或者`RUN ["executable", "param1", "param2"]`。
前者将在shell终端中运行，即`/bin/sh -c`。后者使用`exec`执行。指定使用其它终端执行可以用后一种方式实现。比如`RUN ["/bin/bash", "-c", "echo hello"]`。
每一条`RUN`指令执行的时候都会在当前镜像的基础上创建新的镜像，如果你的命令实在是非常长的话可以使用`\`来分隔。

## 4. CMD
支持三种格式：

+ `CMD ["executable", "param1", "param2"]`使用`exec`执行，推荐方式。
+ `CMD command param1 param2`在`/bin/sh`中执行，提供给需要交互的应用。
+ `CMD ["param1", "param2"]`提供给`ENTRYPOINT`的默认参数。

指定启动容器时执行的命令，每一个Dockerfile只能有一条`CMD`命令，如果有多条的话，只会执行最后一条。如果用户启动容器时指定了运行的命令，则`CMD`命令会被覆盖掉。

## 5. EXPOSE
格式：`EXPOSE <port> [<port>...]`
例如：`EXPOSE 80 443 22`
这条命令告诉容器需要开放的端口号，以提供给互联系统使用，启动容器的时候需要通过`-p`或者`-P`来分配这些端口。使用`-P`的话，系统会随机指定一些端口映射过来，使用`-p`的话，需要自己具体指定端口与上述端口映射。比如：`docker run -d -p 1022:22 -p 1080:80 -p 1443:443 nginx`。

## 6. ENV
格式：`ENV <key> <value>`
就是指定一个环境变量，会被后续的`RUN`命令使用，并在容器运行时保持。比如：

``` sh
ENV MYSQL_MAJOR 5.6
ENV MYSQL_VERSION 5.6.20
RUN curl -SL "http://dev.mysql.com/get/Downloads/MySQL-$MYSQL_MAJOR/mysql-$MYSQL_VERSION-linux-glibc2.5-x86_64.tar.gz" -o mysql.tar.gz
tar -zxvf mysql.tar.gz /usr/local/mysql
```

## 7. ADD
格式：`ADD <src> <dest>`
复制指定的`<src>`到容器的`<dest>`，其中`<src>`可以是Dockerfile所在目录的一个相对路径（文件或目录）；也可以是一个URL；还可以是一个tar文件（自动解压为目录）。

## 8. COPY
格式：`COPY <src> <dest>`
复制本地主机的`<src>`为容器的`<dest>`，目标路径不存在的时候，会自动创建。如果我们使用本地源目录的时候，推荐使用`COPY`命令。

## 9. ENTRYPOINT
格式：`ENTRYPOINT ["executable", "param1", "param2"]`或`ENTRYPOINT command param1 param2`（shell中执行）。
配置容器启动以后执行的命令，并且不能被`docker run`命令提供的参数覆盖。每一个Dockerfile只能有一个`ENTRYPOINT`命令，当存在多个的时候就只有最后一个会生效。

## 10. VOLUME
格式：`VOLUME ["/data"]`
创建一个本地主机或者其他容器挂载的挂载点，一般用来存储数据库和需要保持的数据等等。

## 11. USER
格式：`USER daemon`
指定运行容器时候的用户名或者UID，后续RUN也会使用指定的用户。

## 12. WORKDIR
格式：`WORKDIR /path/to/workdir`
由于Dockerfile中不能使用`cd`命令，所以我们想要在哪个目录下操作的时候就需要切换进来。为后续的`RUN`，`CMD`以及`ENTRYPOINT`指定工作的目录。可以使用多个`WORKDIR`命令，如果后面为**相对路径**，则是针对当前目录确定的。比如：

``` sh
WORKDIR /a
WORKDIR b
WORKDIR c
```
那么最后进入的目录就是`/a/b/c`，记住啦，别搞混了哦！

`参考书籍`：《Docker技术入门与实战》