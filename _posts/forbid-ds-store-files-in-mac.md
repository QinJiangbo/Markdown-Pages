---
title: 禁止Mac下产生.DS_Store文件
date: 2016-11-21 12:05:00
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/mac-finder-thumbnail.jpg
tags:
	- MacOS
	- .DS_Store
categories:
	- 经验感悟
	- 编程经验
keywords:
	- MacOS
	- .DS_Store
---
在你使用u盘拷贝东西到win电脑下使用文件时是不是经常发现在win下许多文件夹下都有`.DS_Store`名称的文件是不是觉得很烦 ，其实`.DS_Store`是OS X系统生成的文件它包含了与文件夹相关的信息 ，但是因为OS X系统默认是隐藏这类文件所以在Mac下交互使用这些文件是没有任何问题的 。
 
### 1.禁止这个文件生成的方法：
 
打开   “终端” （finder －应用程序－实用工具－终端）  输入下面的命令 并 按下 enter 键 运行 ！然后必须注销Mac并重新登陆系统才能生效！！
   
	defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
 
### 2.如果您后悔了觉得 怪怪的 或者 只是暂时 使用 ，可以使用下面的方法恢复回来 ！
打开   “终端” （finder －应用程序－实用工具－终端）  输入下面的命令 并 按下 enter 键 运行 ！然后必须注销Mac并重新登陆系统才能生效！！
 
     defaults delete com.apple.desktopservices DSDontWriteNetworkStores
     
转载自[http://www.macappbox.com/showinfo-35-373-0.html](http://www.macappbox.com/showinfo-35-373-0.html)