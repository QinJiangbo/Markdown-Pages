---
title: 预加载让页面生动起来
date: 2017-02-21 19:36:06
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader.png
tags:
	- HTML
	- 预加载
categories:
	- 开发技术
	- 前端
keywords:
	- HTML
	- 预加载
---
随着现在网站数量的爆炸式增长，越来越多的用户感到了信息爆炸带来的压力。互联网企业如何让自己的网站在如此众多的网站中脱颖而出，不仅需要自身强劲的企业实力，还需要一个好看的“门面”，这里指的是网站的UI或者App的UI。本文主要是探讨网络情况不佳时如何让用户的体验不那么糟糕？虽然这个要求比较急切，但是很多企业根本就不重视，这样是不好滴！今天就跟大家聊一聊网站的预加载。

## 作品赏析
在进行深入的讨论之前我们先看一看国外的优秀网站是如何进行预加载的设计的。这对我们设计自己的网页有非常大的启发。

**TINIA** [点击查看Demo](http://demos.artbees.net/jupiter5/tinia/)
![TINIA](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader.png)

**Orthosie** [点击查看Demo](http://demos.artbees.net/jupiter5/orthosie/)
![Orthosie](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader2.png)

**JOVIAL** [点击查看Demo](http://demos.artbees.net/jupiter5/jovial/)
![JOVIAL](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader3.png)

**Dies-Pater** [点击查看Demo](http://demos.artbees.net/jupiter5/dies-pater/)
![Dies-Pater](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader4.png)

**EUPORIE** [点击查看Demo](http://demos.artbees.net/jupiter5/euporie/)
![EUPORIE](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader5.png)

**TARA** [点击查看Demo](http://demos.artbees.net/jupiter5/tara/)
![TARA](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader6.png)

## 审查代码
在Chrome打开这些网站，在控制台选中正在加载中的页面，我们不难看出，他们都在每一个页面中加入了一段相同的代码。

![审查元素](https://obrxbqjbi.qnssl.com/blog/image/HTML-Preloader-Inspection.png)

图片中代码如下：

``` html
<div class="mk-body-loader-overlay page-preloader" style="background-color: rgb(208, 17, 43); display: none;">
<img alt="Tara Template - Jupiter WordPress Theme" class="preloader-logo" src="http://demos.artbees.net/jupiter5/tara/wp-content/uploads/sites/156/2016/12/icon-04.png" width="109" height="79"> 
<div class="preloader-preview-area">  
	<div class="ball-pulse-sync">
		<div style="background-color: #ffffff"></div>
		<div style="background-color: #ffffff"></div>
		<div style="background-color: #ffffff"></div>
	</div>  
</div>
</div>
```

可以看到，上面的代码是在每一个页面最开始加载的时候执行的，它的组成部分主要是一张背景图（大多是纯色背景，也可以有图案），还有一个加载动画组成。加载动画的形式可以多种多样，在上面的示例图中可以看到。

## 代码实现
预加载主要是通过JavaScript和CSS3一起实现：其中稍微要点挑战性的部分就是动画的实现。还是以上面的代码块为例，来看看它的动画是如何实现的：

CSS实现

``` css
.ball-pulse-sync {
    display: inline-block
}

.ball-pulse-sync>div {
    width: 15px;
    height: 15px;
    border-radius: 100%;
    margin: 2px;
    -webkit-animation-fill-mode: both;
    animation-fill-mode: both;
    display: inline-block
}

.ball-pulse-sync>div:nth-child(1) {
    -webkit-animation: ball-pulse-sync .6s -.21s infinite ease-in-out;
    animation: ball-pulse-sync .6s -.21s infinite ease-in-out
}

.ball-pulse-sync>div:nth-child(2) {
    -webkit-animation: ball-pulse-sync .6s -.14s infinite ease-in-out;
    animation: ball-pulse-sync .6s -.14s infinite ease-in-out
}

.ball-pulse-sync>div:nth-child(3) {
    -webkit-animation: ball-pulse-sync .6s -70ms infinite ease-in-out;
    animation: ball-pulse-sync .6s -70ms infinite ease-in-out
}

@-webkit-keyframes ball-pulse-sync {
    33% {
        -webkit-transform: translateY(10px);
        transform: translateY(10px)
    }

    66% {
        -webkit-transform: translateY(-10px);
        transform: translateY(-10px)
    }

    100% {
        -webkit-transform: translateY(0);
        transform: translateY(0)
    }
}

@keyframes ball-pulse-sync {
    33% {
        -webkit-transform: translateY(10px);
        transform: translateY(10px)
    }

    66% {
        -webkit-transform: translateY(-10px);
        transform: translateY(-10px)
    }

    100% {
        -webkit-transform: translateY(0);
        transform: translateY(0)
    }
}
```

JavaScript实现：

``` js
function addLoadEvent(func) {
    var oldonload = window.onload;
    if (typeof window.onload != 'function') {
        window.onload = func;
    } else {
        window.onload = function() {
            if (oldonload) {
                oldonload();
            }
            func();
        }
    }
}
addLoadEvent(preloader); // preloader为预加载函数，这里大家自己定义
```

## 后记
在预加载页面的实现上，需要知道预加载时发生在什么时候的。一般来说，页面的加载分为以下几个步骤：

1. 解析HTML结构。
2. 加载外部脚本（JavaScript）和样式表文件（CSS）。
3. 解析并执行脚本代码。
4. 构造HTML DOM模型。// ready阶段
5. 加载图片等外部文件。
6. 页面加载完毕。// load阶段

所以，我们可以看到，预加载页面中如果要使用图片的话，我们只能将事件写入到`windows.load()`中去。如果没有图片的加载，大家可以写到`windows.ready()`中去，这样会快一点。


