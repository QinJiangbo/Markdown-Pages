---
title: 利用Docker创建SSH容器连接
date: 2017-10-11 14:28:15
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/docker-family.png
tags:
	- docker
	- centos
	- ssh
categories:
	- 架构师
	- Docker
keywords:
	- docker
	- centos
	- ssh
---
![Docker](https://obrxbqjbi.qnssl.com/blog/image/docker-family.png)

本文主要是记录如何利用Docker创建一个可以进行SSH连接的容器，关于Docker的基础命令大家可以自行找官方文档学习。创建Docker镜像主要有两种方式，一种是`docker commit`，另一种是`Dockerfile`，第一种的弊端非常明显，因为它不能适应快速变化的部署环境，需要进行手动操作，然后提交到镜像当中；而后一种则可以快速适应变化，只要有Docker的地方它就可以很好地运行。所以，我们来看一下如何利用CentOS7来创建一个可以连接SSH的容器吧！

``` sh
# This Dockerfile is based on centos image
# Version: 2017.10.10
# Author: QinJiangbo
# Command Format: Instruction [arguments / command] ..

# 第一行必须指定基于的基础镜像
FROM centos

# 维护者信息
MAINTAINER qinjiangbo<qinjiangbo1994@163.com>

# 安装ssh
RUN yum install passwd openssl openssh-server -y && yum clean all
RUN ssh-keygen -A
RUN mkdir -p /root/.ssh
ADD authorized_keys /root/.ssh/authorized_keys
RUN chown -R root:root /root/.ssh
RUN chmod -R 700 /root/.ssh

# 开放端口22
EXPOSE 22

# 容器启动时执行指令
CMD ["/usr/sbin/sshd", "-D"]
```

在执行这个脚本之前，需要在宿主机上先生成一个ssh文件，然后拷贝到当前目录[`/root/sshd`]

``` sh
sudo ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub > authorized_keys
```

编译镜像：

``` sh
docker build -t sshd .
```
注意结尾是有一个`.`号的，表示当前目录。

然后再执行脚本：

``` sh
docker run -d -p 2222:22 sshd
```

最后在本地通过ssh就可以进入容器了：

``` sh
ssh root@localhost -p 2222
Last login: Wed Oct 11 06:48:02 2017 from gateway
[root@85a58df25d9c ~]# 
```
