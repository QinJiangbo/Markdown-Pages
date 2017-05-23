---
title: Github Pages自定义域名+CDN加速解决方案
date: 2016-10-17 22:55:24
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/gp-thumbnail.jpg
tags:
	- Github Pages
	- Git
categories:
	- 经验感悟
keywords:
	- Github Pages
	- Git
---
## 先说说烦恼
自CSDN搬家到Github Pages以来，倍感欣喜！原因很简单，省了我每个月一两百的服务器费用。但是随之而来的也存在一个问题，那就是Github Pages的托管服务器是在美国本土的，没有一个在海外。所以，大家平时访问我的网站都是通过浏览器直接访问美国的github服务器实现的，路程远不说，还有天朝的大墙时不时封一些服务器，其结果可想而知。面对博客的访问速度超过12s，患有严重强迫症的博主只能表示需要哭一会先。

## 没有长夜痛哭过的经历不足以谈人生
哈哈，好吧！这句话是我跟室友聊天的时候突然想到的，就做这里的一个标题吧。现在面临两个选择，一个是坚持使用Github提供的服务，继续使用它的二级域名qinjiangbo.github.io，使用https加密；另一个是自己搞一个域名，在国内解析速度会快很多，但是没有https协议加密了。思考再三，觉得好像也没有多少人觉得一个https协议会比网页的加载速度更重要，所以，博主决定采用自己购买的顶级域名qinjiangbo.com。下面就来说一说如何将github.io映射到自己的域名当中去。

## 映射自定义域名
一、 进入你的github主页，找到你的用户名username.github.io的项目，如果没有，赶紧自己创建一个，至于如何创建，这不在本文的论述的范围之内，所以不做解释了。
二、 在**更改设置之前**你需要先ping一下username.github.io，**记住这个ping得到的IP地址**，后面会使用到。
三、 在username.github.io项目中找到Settings设置，点击进入设置。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-01.png)

四、找到自定义域名这一项，Custom Domains，输入你的域名，注意，是不带www或者blog的。直接就是xxx.com。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-02.png)

## 添加自定义域名的解析
一、进入控制台，找到域名这一项服务。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-03.png)

二、进入域名解析，使用前面得到的IP地址添加一条A记录。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-04.png)

## 使用CDN加速
### 前提
你需要开通两项服务：

- OSS对象存储服务
- CDN加速服务

### 具体步骤如下
一、切入阿里云的总控制台，选择对象存储服务，没有开通的先开通这项服务，进入这项服务，然后创建一个桶（bucket），用于后面装在静态资源文件。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-05.png)

二、进入CDN服务，没有开通的也要先开通，添加一个新的域名。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-06.png)

三、解析的时候选择图片小文件，基本上可以说网页文件（5M一下）都是小文件，然后选择之前创建的OSS桶。

![](https://obrxbqjbi.qnssl.com/blog/image/gh-pages-07.png)

四、点击下一步，大功告成！

## 总结
经过这次的“折腾”，终于是将网站的速度提升了，现在打开速度2s以内，嗖嗖的！希望本教程对你有所帮助！