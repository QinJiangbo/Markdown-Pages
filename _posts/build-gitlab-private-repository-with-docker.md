---
title: 利用Docker建立Gitlab私服
date: 2017-10-13 21:13:17
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/gitlab-logo.jpeg
tags:
	- docker
	- gitlab
categories:
	- 架构师
	- Docker
keywords:
	- docker
	- gitlab
---
![Gitlab](https://obrxbqjbi.qnssl.com/blog/image/gitlab-logo.jpeg)
现在越来越多的公司开始使用Git来进行协作开发了，那么如何来管理项目中的代码呢？目前市面上做的非常优秀的工具有**Github**，几乎每一个开发人员都知道它。国内的有**码云**，也是一个非常优秀的代码托管平台。但是企业内部的代码不可能放到这些第三方平台托管，因为风险太大了，万一它们没处理好，我们的数据几乎等于和盘托出。所以，Gitlab顺势而生！今天重点介绍如何利用Docker搭建一个内部Gitlab代码托管平台。

## 安装Gitlab镜像
使用`docker search gitlab`命令查询一下：

``` sh
docker search gitlab
NAME                                         DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
gitlab/gitlab-ce                             GitLab Community Edition docker image base...   1417                                    [OK]
sameersbn/gitlab                             Dockerized gitlab web server                    947                                     [OK]
gitlab/gitlab-runner                         GitLab CI Multi Runner used to fetch and r...   230                                     [OK]
gitlab/gitlab-ee                             GitLab Enterprise Edition docker image bas...   69
```
以上是前5条记录，我们需要下载第一条记录。可以看到这个是一个官方镜像，而且我告诉你哦，这个镜像有1G以上，所以，是不是需要搞一个代理呢？待会儿会说明具体如何操作。设置好代理之后，我们可以通过`docker pull gitlab/gitlab-ce`这个命令将镜像拉取下来。

## 添加Aliyun代理
我使用的系统是MacOS High Sierra，所以，我直接在Docker工具里面添加了。你需要先到Aliyun注册一个账号（如果有的话当我没说），然后开通容器服务。具体步骤如下：

1. 开通服务：[https://www.aliyun.com/product/containerservice](https://www.aliyun.com/product/containerservice)
![](https://obrxbqjbi.qnssl.com/blog/image/aliyun-container-service.png)
2. 选择镜像仓库控制台：[https://cs.console.aliyun.com/](https://cs.console.aliyun.com/)
![](https://obrxbqjbi.qnssl.com/blog/image/container-console.png)
3. 获取镜像加速地址：[https://cr.console.aliyun.com/](https://cr.console.aliyun.com/)
![](https://obrxbqjbi.qnssl.com/blog/image/container-mirror-accelerate.png)

我的代理地址是：`https://9cc5rund.mirror.aliyuncs.com`。设置代理的方式如下：
![](https://obrxbqjbi.qnssl.com/blog/image/docker-registry-proxy.png)
下面的`registry-mirrors:No certs for...`警告可以忽略。

## 修改Gitlab内部配置
下载好镜像之后，我们需要将其run起来。命令如下：

``` sh
$ docker run -d -p 80:80 -p 22:22 -p 443:443 gitlab/gitlab-ce
```
然后使用`docker ps -a`命令查看容器情况：

``` sh
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                 PORTS                                                          NAMES
e21b83825a9d        gitlab/gitlab-ce    "/assets/wrapper"   29 hours ago        Up 4 hours (healthy)   0.0.0.0:22->22/tcp, 0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   practical_engelbart
```

这里需要进入容器修改一下里面的一个网络配置，否则后面我们`git clone`的时候可能没法连接。

``` sh
$ docker exec -it e21 vim /etc/gitlab/gitlab.rb
```
修改`gitlab.rb`：

``` rb
## GitLab configuration settings
##! This file is generated during initial installation and **is not** modified
##! during upgrades.
##! Check out the latest version of this file to know about the different
##! settings that can be configured by this file, which may be found at:
##! https://gitlab.com/gitlab-org/omnibus-gitlab/raw/master/files/gitlab-config-template/gitlab.rb.template


## GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
# external_url 'GENERATED_EXTERNAL_URL'
external_url 'http://192.168.31.145/'

## Legend
##! The following notations at the beginning of each line may be used to
##! differentiate between components of this file and to easily select them using
##! a regex.
##! ## Titles, subtitles etc
##! ##! More information - Description, Docs, Links, Issues etc.
```
上面的`192.168.31.145`是本机的ip地址。设置完毕以后重启容器：

``` sh
$ docker restart e21
```
OK！

## 运行应用
我们打开地址`http://localhost`，80端口可以省略端口号的。可能一开始会出现502的画面，就像这样：
![](https://obrxbqjbi.qnssl.com/blog/image/gitlab-502-page.png)
没关系，那是因为Gitlab的各项系统服务正在启动，过一会儿刷新就好啦。然后会要求设置新的密码，设置完成以后我们就进入了Gitlab的页面。
![](https://obrxbqjbi.qnssl.com/blog/image/gitlab-index-page.png)
创建一个项目`Hexo-Workflow`，如下：
![](https://obrxbqjbi.qnssl.com/blog/image/gitlab-project-page.png)
其中我们可以看到`git clone`的地址是`git@192.168.31.145:Richard/Hexo-Workflow.git`，在我们进行git clone之前，我们需要向Gitlab添加一下我们本机的SSH Key实现免登。具体页面如下：
![](https://obrxbqjbi.qnssl.com/blog/image/gitlab-ssh-key.png)
好的，添加完成以后，我们就可以在本地把这项目拉取下来啦。

``` sh
$ git clone git@192.168.31.145:Richard/Hexo-Workflow.git
Cloning into 'Hexo-Workflow'...
The authenticity of host '192.168.31.145 (192.168.31.145)' can't be established.
ECDSA key fingerprint is SHA256:SYHTQxwLpe9OaeQW6f/QRhtQDnXl+zkaQVtZzeObjGQ.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.31.145' (ECDSA) to the list of known hosts.
remote: Counting objects: 13, done.
remote: Total 13 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (13/13), 19.95 KiB | 6.65 MiB/s, done.
Resolving deltas: 100% (2/2), done.
```
然后就和在Github提交代码等操作是一样。

## 总结
Gitlab作为一种非常优秀的代码托管工具已经被广泛地应用到了各个大公司，一般是作为企业内部的代码托管工具。

![](https://obrxbqjbi.qnssl.com/blog/image/gitlab-enterprise.png)

搭建内部代码托管平台的好处不言而喻，关键是非常地易于管理，给管理员有很大的自主性，而不是受制于第三方平台的约束。