---
title: macOS下pip3安装scrapy错误
date: 2017-02-24 14:35:55
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/scrapy-logo.jpg
tags:
	- scrapy
	- error
categories:
	- 开发技术
	- Python
keywords:
	-scrapy
	error
---
![](https://obrxbqjbi.qnssl.com/blog/image/scrapy-logo.jpg)

一直比较依赖IDE，是一个工具控，因为好的工具能提升工作的效率。但是今天发现PyCharm有一个设置把我给坑了（咋不说你自己不小心呢？）。就是使用PyCharm安装Python的第三方依赖包的时候发生了一个神奇的事情。正如标题所说，安装scrapy的标准做法是：

``` bash
pip3 install scrapy
```

但是我使用PyCharm直接安装的，发现并不能像官网那样直接使用`scrapy`命令来创建项目。而是报了一个错：

``` bash
zsh: command not found: scrapy
```

怎么会呢？？？明明刚刚装了的啊？？？查看项目结构发现一个严重的问题：就是位置安装错误。先看项目结构：

![](https://obrxbqjbi.qnssl.com/blog/image/scrapy-project.png)

居然存在两个site-packages目录！！！仔细一看，下面这个有一个`library root`提示。莫非是安装在了用户的目录下，导致环境变量中找不到这个包的路径。肯定是用PyCharm安装的时候装错了。再看PyCharm的设置，果然是的。

![](https://obrxbqjbi.qnssl.com/blog/image/scrapy-settings.png)

需要将上图中的这个**install to user's site packages directory (/Users/Richard/.local)**勾掉（就是让它不选中），然后将Scrapy卸载重新安装就可以了。其他的包如果没有在命令行下启动的需求的话就勾选这个选项，对项目没影响的。

``` bash
→ scrapy
Scrapy 1.3.2 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  commands      
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```
