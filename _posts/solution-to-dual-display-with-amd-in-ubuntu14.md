---
title: Ubuntu14.04安装AMD显卡驱动双屏显示器完全解决方案
date: 2016-05-27 00:20:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/ubuntu-thumbnail.jpg
tags:
	- Ubuntu
	- AMD
	- Display
categories:
	- 开发技术
keywords:
	- Ubuntu
	- AMD
---
![](https://obrxbqjbi.qnssl.com/blog/image/ubuntu-thumbnail.jpg)

网上有很多方法，但是针对AMD显卡的方案不多，所以笔者今天想写一篇关于**AMD显卡**的教程。

首先，进入这个网址下载一些东东：[http://support.amd.com/zh-cn/download/desktop?os=Ubuntu+x86+64] (http://support.amd.com/zh-cn/download/desktop?os=Ubuntu+x86+64)，说明一下，这个网址是针对Ubuntu64位系统的，32位的用户请选择32位的文件下载。下哪些文件呢？

有三个：

1. AMD Catalyst™ 14.12 Proprietary Ubuntu 14.04 x86_64 Video Driver for Graphics Accelerators 大概是52MB

2. AMD Catalyst™ 14.12 Proprietary Ubuntu 14.04 x86_64 Minimal Video Driver for Graphics Accelerators (Non-X Support) 大概是61MB

3. AMD Catalyst™ 14.12 Proprietary Ubuntu 14.04 x86_64 Catalyst Control Center

这三个文件是很重要的，相信读者朋友们看到此文时这几个文件有了相应的更新，大家子集注意一下下载对应的文件即可。

首先我们要先安装第二个文件（Non-X Support），这个是可以直接安装不需要依赖的，下载下来以后这些都是deb包，所以双击用新立得安装即可，接着安装第一个文件，其次是第三个（这是一个图形化管理工具，可以在里面设置显示器分辨率，不过在我这儿好想不太管用，如果你的可以的话，那就直接设置就好了，打开命令是\# amdcccle。如果不行的话，接着往下看。

好的，下面我相信大家可以输入一下命令了

``` bash
# xrandr
```

这是一个很重要的显示器分辨率管理工具

当输入这个命令时，我们会得到显示器的相应的信息如下：

``` bash
Screen 0: minimum 320 x 200, current 1366 x 768, maximum 8192 x 8192
LVDS connected primary 1366x768+0+0 (normal left inverted right x axis y axis) 309mm x 174mm
   1366x768       60.0*+
   1360x768       60.0  
   1280x768       60.0  
   1280x720       60.0  
   1024x768       60.0  
   1024x600       60.0  
   800x600        60.0  
   800x480        60.0  
   640x480        60.0  
DFP1 disconnected (normal left inverted right x axis y axis)
CRT1 disconnected (normal left inverted right x axis y axis)
```

解释一下，LVDS是你的笔记本的显示器，DFP1和CRT1是外接显示器，接上显示器的时候看这两个中哪个连接上了就用哪个。

笔者的是CRT1。

下面介绍几个命令：

为了增加你所需要的分辨率，可以用（1920*1080为例）

``` bash
# cvt 1920 1080
```
得到如下输出：

``` bash
# 1920x1080 59.96 Hz (CVT 2.07M9) hsync: 67.16 kHz; pclk: 173.00 MHz
Modeline "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
```

那么，下面这个Modeline后面的这一串就是我们要添加的显示器分辨率模式

好了，得到以后我们要新建这个模式呀，用这个命令：

``` bash
# xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
```

然后这个分辨率就新建成功了，下面我们要把它添加进去

``` bash
# xrandr --addmode CRT1(这个是我的型号，大家对应上) "1920x1080_60.00"
```

好的，添加成功，到了最后一步啦！应用到这个显示器上面去！

``` bash
# xrandr --output CRT1 --mode "1920x1080_60.00"
```

发现你的屏幕是不**duang,duang** ~ 一下子就变成了你想要的分辨率呢？

好啦，就写这么多了，有什么问题大家可以在下面留言～