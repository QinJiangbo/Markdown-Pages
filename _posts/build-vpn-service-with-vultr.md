---
title: 利用Vultr搭建科学上网工具
date: 2017-10-30 15:24:34
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Vultr-Logo.png
tags:
	- Vultr
	- 科学上网
	- VPN
	- ShadowSocks
categories:
	- 经验感悟
	- 编程经验
keywords:
	- Vultr
	- 科学上网
	- VPN
	- ShadowSocks
---
![Vultr](https://obrxbqjbi.qnssl.com/blog/image/Vultr-Logo.png)

其实网上关于搭建科学上网的文章非常多，只不过百度搜索的时候没显示出来罢了，做这篇文章的目的主要是验证最新的科学上网服务搭建实践。首先当然是选择国外的服务商了，我没有选择亚马逊是因为它连接我电脑太慢了（其他人电脑估计不一样）。下面列举两个国外性价比比较高的服务商：

+ Vultr
+ Digital Ocean

大家可以去百度上搜索这两家公司。本文以第一家公司的产品为例，讲解一下科学上网环境如何搭建。

## 搭建服务器
具体配置信息可以选择下面的实例，因为ShadowSocks本身并不会消耗太多的服务器资源，所以我选择最优惠的那种类型（估计这种就是为了科学上网服务的，哈哈）。

![](https://obrxbqjbi.qnssl.com/blog/image/vultr-deploy-servers.png)

搭建好服务器以后我们需要登录服务器做一些操作：

``` sh
ssh root@[your_server_ip]
```
注意，服务器密码在服务详情里面：

![](https://obrxbqjbi.qnssl.com/blog/image/vultr-server-detail.png)

## 安装ShadowSocks
其实可以参考ShadowSocks在Github上的官方教程：[https://github.com/shadowsocks/shadowsocks/tree/master](https://github.com/shadowsocks/shadowsocks/tree/master)

![](https://obrxbqjbi.qnssl.com/blog/image/shadowsocks-github-tutorials.png)

我们就直接输入命令安装啦：

``` sh
apt-get install python-pip
## 这个必须先安装，否则会报错 
wget https://bootstrap.pypa.io/ez_setup.py -O - | python
pip install shadowsocks
```
好啦，安装完成！

## 服务端启动服务
启动服务的命令也非常简单：

前台启动：

``` sh
ssserver -p 443 -k password -m aes-256-cfb
```

后台启动：

``` sh
sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start
```

上面的`password`要替换成你自己的密码，随便什么都可以，只不过别太简单。后面的`nobody`这个可以忽略，它是指以什么身份启动，这里默认就可以了。

## 客户端配置服务
我使用的系统是MacOS High Sierra，所以我使用的客户端是[ShadowsocksX-2.6.3.dmg](https://github.com/shadowsocks/shadowsocks-iOS/releases/download/2.6.3/ShadowsocksX-2.6.3.dmg)。配置方式如下：
![](https://obrxbqjbi.qnssl.com/blog/image/macOS-shadowsocks-ng-config.png)

## 访问目标网页
访问一下Google试试：
![](https://obrxbqjbi.qnssl.com/blog/image/shadowsocks-google-index-page.png)

Bingo！成功了！说明我们的配置是OK的！

## 总结
关于科学上网方案，其实有很多种选择，VPN和ShadowSocks都是使用比较多的情形，其实现在主流的服务提供商都是提供VPN服务。作为消费者本身来说，选择哪一种方式完全取决于当前的实际需要，其实我们也可以找一些ShadowSocks服务提供商（貌似特别难找，如果有的话欢迎在下面评论区留言，感激不尽！）。我自己之前使用的是DEVPN，但是七块钱一个月才分给我10G流量，不能忍，直接自己搭建一个多舒服！2.5美刀500G多划算。