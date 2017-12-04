---
title: macOS下Vim配色方案
date: 2017-10-11 10:50:18
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/solarized-yinyang.png
tags:
	- solarized
	- vim
	- 配色
categories:
	- 架构师
	- Linux
keywords:
	- solarized
	- vim
	- 配色
---
![solarized](https://obrxbqjbi.qnssl.com/blog/image/solarized-yinyang.png)

最近重装了macOS系统，结果以前的配置全部丢失了，然后又按照官网的要求重新安装了一下VIM的主题。结果发现一直和官方的配色对不上，不知道啥情况。之前的配置是这样的：

``` vim
syntax enable
set background=dark
colorscheme solarized
```
对应的配色情况如下：
![之前的配色](https://obrxbqjbi.qnssl.com/blog/image/vim-color-before.png)
什么鬼？！不是说好的靛青色背景吗？！官方也一直没有给出正面的回复，去Github主页查找也没有查找到对应的解决方案，直到找到一个论坛，也遇到了和我一样的问题，受他们回复的启发，我尝试加了两行配置，如下：

``` vim
syntax enable
set background=dark
let g:solarized_termcolors=16
let g:solarized_termtrans=1
colorscheme solarized
```
添加完成，效果果然出来了，手动比上金星老师的标志性动作，完！美！
![后面的配色](https://obrxbqjbi.qnssl.com/blog/image/vim-color-after.png)

贴上论坛的地址：
[http://bbs.feng.com/read-htm-tid-10995409-page-3.html](http://bbs.feng.com/read-htm-tid-10995409-page-3.html)