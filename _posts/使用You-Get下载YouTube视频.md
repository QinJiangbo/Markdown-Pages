---
title: 使用You-Get下载YouTube视频
date: 2017-03-15 19:13:09
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/you-get-thumbnail.png
tags:
	- You-Get
	- YouTube
categories:
	- 架构师
	- Linux
keywords:
	- You-Get
	- YouTube
---
![You-Get](https://obrxbqjbi.qnssl.com/blog/image/you-get-thumbnail.png)
在线网站有很多，但是提供下载链接的很少。一般都是会采取下载客户端，要求你在客户端缓存的策略，但是有没有什么方法可以让你不在客户端就可以直接在PC端（包括Linux端 & Mac端）下载呢？答案是肯定的，You-Get便是其中的一种选择。

## 什么是You-Get？
You-Get是一个非常轻量级的命令行实用工具，可以用来从网上下载媒体内容（比如视频，音频，图片）。当没有其它什么方便的途径下载的时候它是一个不错的选择。

**看看它能做什么？给定一条视频的播放地址，它就能自动读取视频流并解析然后下载。**

``` bash
$ you-get http://www.fsf.org/blogs/rms/20140407-geneva-tedx-talk-free-software-free-society
Site:       fsf.org
Title:      TEDxGE2014_Stallman05_LQ
Type:       WebM video (video/webm)
Size:       27.12 MiB (28435804 Bytes)

Downloading TEDxGE2014_Stallman05_LQ.webm ...
100.0% ( 27.1/27.1 MB) ├████████████████████████████████████████┤[1/1]   12 MB/s
```

据说you-get常规的下载速度比迅雷要好很多！当然，如果迅雷会员开挂了就不好说啦。

## You-Get如何安装？
先说说You-Get的依赖条件。它需要你提前安装好Python3和FFmpeg的运行时环境。切记。

### 安装方式1
使用python的pip工具安装

``` bash
$ pip3 install you-get
```

### 安装方式2
源码安装

``` bash
$ [sudo] python3 setup.py install
```

### 安装方式3 （仅支持Mac平台）
使用Homebrew安装

``` bash
$ brew install you-get
```

### 安装方式4 （仅支持Win平台）
使用Chocolatey安装

``` bash
> choco install you-get
```

## 下载YouTube视频
我们需要准备视频的地址，比如：`https://www.youtube.com/watch?v=571IIGJKeoA&index=2&list=PL0Smm0jPm9WcCsYvbhPCdizqNKps69W4Z`，然后在命令行直接输入命令：

``` bash
$ you-get https://www.youtube.com/watch?v=571IIGJKeoA***
```

这样就能下载啦。如果你需要查看所下载的视频有哪几种清晰度？方便！使用`you-get`命令的`-i`选项即可。

``` bash
$ you-get -i 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
site:                YouTube
title:               Me at the zoo
streams:             # Available quality and codecs
    [ DEFAULT ] _________________________________
    - itag:          43
      container:     webm
      quality:       medium
      size:          0.5 MiB (564215 bytes)
    # download-with: you-get --itag=43 [URL]

    - itag:          18
      container:     mp4
      quality:       medium
    # download-with: you-get --itag=18 [URL]

    - itag:          5
      container:     flv
      quality:       small
    # download-with: you-get --itag=5 [URL]

    - itag:          36
      container:     3gp
      quality:       small
    # download-with: you-get --itag=36 [URL]

    - itag:          17
      container:     3gp
      quality:       small
    # download-with: you-get --itag=17 [URL]
```
我们可以看到上面`[DEFAULT]`默认选项。一般来说，视频总是会下载清晰度最高的那个，如果你需要修改下载的清晰度，可以使用`you-get --itag={NUMBER} [URL]`命令。

### 批量下载视频
将需要下载的视频链接地址写入一个文件，像这样：

**videoList.txt**

``` txt
https://www.youtube.com/watch?v=gNkE3tFUFuw&index=1
https://www.youtube.com/watch?v=571IIGJKeoA&index=2
https://www.youtube.com/watch?v=hfO6iRj-GZo&index=3
https://www.youtube.com/watch?v=iDsywHN-u08&index=4
https://www.youtube.com/watch?v=n1qyTXRdWQg&index=5
https://www.youtube.com/watch?v=5lGPkRDWTA8&index=6
https://www.youtube.com/watch?v=2i9LdgTbcg8&index=7
...
https://www.youtube.com/watch?v=ya5fm3-d_SU&index=112
```

批量下载**downloader.sh**

``` bash
#!/bin/bash
# -*- encoding: utf-8 -*-

for line in `cat videoList.txt`
do
    you-get $line
done
```
赋予**downloader.sh**可执行权限，再执行如下命令就可以下载啦。

``` bash
$ sudo chmod 755 downloader.sh
$ sh downloader.sh
```

## You-Get支持哪些视频网站？

|Site |	URL|	Videos?|     Images?|	Audios?|
|:------:|------|:--------:|:---------:|:---------:|
|**YouTube**|https://www.youtube.com/	|✓		
|**Twitter**|https://twitter.com/	|✓	|✓	
|VK	|http://vk.com/	|✓	|✓	
|Vine	|https://vine.co/	|✓		
|Vimeo	|https://vimeo.com/	|✓		
|Vidto	|http://vidto.me/	|✓		
|Videomega	|http://videomega.tv/	|✓		
|Veoh	|http://www.veoh.com/	|✓		
|**Tumblr**|https://www.tumblr.com/	|✓	|✓	|✓
|TED	|http://www.ted.com/	|✓		
|SoundCloud	|https://soundcloud.com/	| |		|✓
|SHOWROOM	|https://www.showroom-live.com/	|✓
|Pinterest	|https://www.pinterest.com/|		|✓	
|MusicPlayOn	|http://en.musicplayon.com/	|✓		
|MTV81	|http://www.mtv81.com/	|✓		
|Mixcloud	|https://www.mixcloud.com/||			|✓
|Metacafe	|http://www.metacafe.com/	|✓		
|Magisto	|http://www.magisto.com/	|✓		
|Khan Academy	|https://www.khanacademy.org/	|✓		
|Internet Archive	|https://archive.org/	|✓		
|**Instagram**	|https://instagram.com/	|✓	|✓	
|InfoQ	|http://www.infoq.com/presentations/	|✓		
|Imgur	|http://imgur.com/	|	|✓	
|Heavy Music Archive	|http://www.heavy-music.ru/		||	|✓
|**Google+**	|https://plus.google.com/	|✓	|✓	
|Freesound	|http://www.freesound.org/	||		|✓
|Flickr	|https://www.flickr.com/	|✓	|✓	
|FC2 Video	|http://video.fc2.com/	|✓		
|Facebook	|https://www.facebook.com/	|✓		
|eHow	|http://www.ehow.com/	|✓		
|Dailymotion	|http://www.dailymotion.com/	|✓		
|CBS	|http://www.cbs.com/	|✓		
|Bandcamp	|http://bandcamp.com/	||		|✓
|AliveThai	|http://alive.in.th/	|✓		
|interest.me	|http://ch.interest.me/tvn	|✓	||	
|**755 <br> ナナゴーゴー**|http://7gogo.jp/	|✓	|✓	
|**niconico <br> ニコニコ動画**|http://www.nicovideo.jp/	|✓		
|**163 <br> 网易视频 <br> 网易云音乐**|http://v.163.com/ <br> http://music.163.com/	|✓	|	|✓
|56网	|http://www.56.com/	|✓		
|**AcFun**|http://www.acfun.tv/	|✓		
|**Baidu <br> 百度贴吧**|http://tieba.baidu.com/	|✓	|✓	
|爆米花网	|http://www.baomihua.com/	|✓		
|**bilibili <br> 哔哩哔哩**|http://www.bilibili.com/	|✓		
|Dilidili	|http://www.dilidili.com/	|✓		
|豆瓣	|http://www.douban.com/	|✓	|	|✓
|斗鱼	|http://www.douyutv.com/	|✓		
|Panda <br> 熊猫	|http://www.panda.tv/	|✓		
|凤凰视频	|http://v.ifeng.com/	|✓		
|风行网	|http://www.fun.tv/	|✓		
|iQIYI <br> 爱奇艺	|http://www.iqiyi.com/	|✓		
|激动网	|http://www.joy.cn/	|✓		
|酷6网	|http://www.ku6.com/	|✓		
|酷狗音乐	|http://www.kugou.com/	||		|✓
|酷我音乐	|http://www.kuwo.cn/		||	|✓
|乐视网	|http://www.le.com/	|✓		
|荔枝FM	|http://www.lizhi.fm/			|✓
|秒拍	|http://www.miaopai.com/	|✓		
|MioMio弹幕网	|http://www.miomio.tv/	|✓		
|痞客邦	|https://www.pixnet.net/	|✓		
|PPTV聚力	|http://www.pptv.com/	|✓		
|齐鲁网	|http://v.iqilu.com/	|✓		
|QQ <br> 腾讯视频	|http://v.qq.com/	|✓		
|企鹅直播	|http://live.qq.com/	|✓		
|Sina <br> 新浪视频 <br> 微博秒拍视频	|http://video.sina.com.cn/
|http://video.weibo.com/	|✓		
|Sohu <br> 搜狐视频	|http://tv.sohu.com/	|✓		
|**Tudou <br> 土豆** |http://www.tudou.com/	|✓		
|虾米	|http://www.xiami.com/	||		|✓
|阳光卫视	|http://www.isuntv.com/	|✓		
|**音悦Tai**	|http://www.yinyuetai.com/	|✓		
|**Youku <br> 优酷**	|http://www.youku.com/	|✓		
|战旗TV	|http://www.zhanqi.tv/lives	|✓		
|央视网	|http://www.cntv.cn/	|✓		
|花瓣	|http://huaban.com/	|	|✓	
|Naver <br> 네이버	|http://tvcast.naver.com/	|✓		
|芒果TV	|http://www.mgtv.com/	|✓		
|火猫TV	|http://www.huomao.com/	|✓		
|全民Tv	|http://www.quanmin.tv/	|✓

## 另记
本文知识做了一个简单的介绍，关于具体的介绍大家不妨访问`you-get`的官方网站。地址为[https://you-get.org/](https://you-get.org/)。有什么使用上的问题可以直接在Github的ISSUE里面直接提BUG。
