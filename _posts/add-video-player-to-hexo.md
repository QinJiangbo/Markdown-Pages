---
title: Hexo新增视频播放功能
date: 2017-07-11 00:23:25
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/youku-video.png
tags: 
	- 视频
categories:
	- 经验感悟
keywords:
	- 视频
---
今天逛了一下[hexo.io](https://hexo.io)的官方插件市场，发现一个插件挺不错，名字叫**hexo-tag-tencent**，同时支持优酷和腾讯视频的播放，很方便。

### 安装方式

``` bash
npm install --save hexo-tag-tencent
```

### 使用方式
`作者原话`：markdown 文档直接将视频分享代码粘贴就可以插入视频。

### 优酷视频
<iframe height=500 width=100% src='http://player.youku.com/embed/XMTYyODcyMjgyNA==' frameborder=0 'allowfullscreen'></iframe>

### 腾讯视频
<iframe frameborder="0" width="100%" height="500" src="https://v.qq.com/iframe/player.html?vid=z0345uxzyjw&tiny=0&auto=0" allowfullscreen></iframe>

### 纯H5视频
<video controls="controls" width=100% height=500>
  <source src="http://www.listeningexpress.com/cnn/cnnstudentnews/CNNSN%202017-06-02%20CNN%20Student%20News.mp4" type="video/mp4" />
Your browser does not support the video tag.
</video>

### 更新
发现`hexo-tag-tencent`插件并没有做什么有用的事情，就是将ID改了一下加入进来，拼接了字符串，所以直接将其删除了。