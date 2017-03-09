---
title: Hexo中Marked.js与MathJax共存问题
date: 2016-08-14 22:26:35
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/latex-thumbnail.jpg
tags:
	- LaTEX
	- Hexo-Math
categories:
	- 经验感悟
keywords:
	- MathJax
	- Hexo博客
---
## 使Marked.js与MathJax共存
因此我提供一个修改marked.js源码的方式来避开这些问题 

- 针对下划线的问题，我决定取消\_作为斜体转义，因为marked.js中\*也是斜体的意思，所以取消掉_的转义并不影响我们使用markdown，只要我们习惯用\*作为斜体字标记就行了。 
- 针对marked.js与Mathjax对于个别字符二次转义的问题，我们只要不让marked.js去转义\\\,\\{,\\}在MathJax中有特殊用途的字符就行了。 

具体修改方式，用编辑器打开marked.js（在./node_modules/hexo-renderer-marked/node_modules/marked中）

Step1:

``` javascript
escape: /^\\([\\`*{}\[\]()# +\-.!_>])/,
```
替换成

``` javascript
escape: /^\\([`*\[\]()# +\-.!_>])/,
```
这一步是在原基础上取消了对\\\,\\{,\\}的转义(escape)

Step2:

``` javascript
em: /^\b_((?:[^_]|__)+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```
替换成

``` javascript
em:/^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

这样一来MathJax就能与marked.js共存了。
