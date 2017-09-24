---
title: Velocity编程指南（一）
date: 2017-06-18 22:49:28
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/velocity-thumbnail.png
tags:
	- Velocity
	- 模板引擎
categories:
	- 开发技术
	- Java
keywords:
	- Velocity
	- 模板引擎
---
最近在看一个模板引擎工具Velocity，是Apache项目下面的一个子项目，已经非常出名了！不过我之前倒没有接触过这个模板引擎，我用的是另一个模板引擎[freemarker](http://freemarker.org/)，这也是Apache下面的一个项目。两者都非常优秀，原理都差不多，大家可以根据项目的需要选择其中一个学习。

## 什么是Velocity?
先回答第一个问题，什么是Velocity？嗯，**Velocity就是一个基于Java的模板引擎。它允许网页设计者引用Java代码中定义的方法，模板的开发者和Java后台的开发者可以在MVC模式下并行地工作，这样能极大地提升效率。**除了生成网页代码以外，Velocity能支持生成SQL，特定格式的文本以及其它任何你想生成的文本格式。

## 定义语言VTL
VTL就是Velocity Template Language的简称，是Velocity的模板语言。本系列的博客主要就是要讨论Velocity的语法及其使用方式。本系列的博客中所使用的例子哈，基本上都是网页中的动态嵌入文件，但是呢，我们前面说到过，Velocity的用法和潜力远远不止如此！

## 开始旅程
好了，唠叨完这一些以后，我们正式地开始这段旅程哈！本系列共分为五章，本文是第一章，后面中间三章讲语法，最后一章实战！