---
title: GitHub Pages入门指南
date: 2016-08-25 23:43:17
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/gp-thumbnail.jpg
tags:
	- Git
	- Cheat Sheet
categories:
	- 经验感悟
keywords:
	- Git
	- Cheat Sheet
---
Github Pages是能通过Github轻松发布以及托管的公开网页。最快上手并且使用的方式就是通过页面自动生成器为我们生成一些HTML和CSS文件。然后你就可以通过浏览器或者本机电脑远程修改Github Pages里面的内容以及样式。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-1-content.png)

## 创建你自己的网站
一旦你已经[登录](https://github.com/login)了，你就可以开始创建一个上手的资源库。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-2-content.png)

在新的资源库的页面上，你需要指定这个资源库的名字从而生成你的网站。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-3-content.png)

你的网站的HTML和CSS资源将会被存放在名为`username.github.io` 的资源库中（其中 “username”是你的github用户名）。为了得到一组最基础的HTML和CSS资源，你需要打开设置标签页然后开启页面自动生成器功能。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-4-content.png)

如果你滚动鼠标到设置页面，你就会看到**Automatic Page Generator**这样一个按钮。通过点击这个按钮，你就开始自动生成这个网站的内容了。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-5-content.png)

一旦你已经点击了这个按钮，你将会被重定向到这个生成步骤的第一步：内容。你可以暂时保持默认的内容，之后可以修改这个内容。然后让我们继续，点击底部的按钮“Continue to Layouts”。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-6-content.png)

现在，你需要选择你的主题。这部分不需要太多思考，选择你喜欢的主题即可，如果想改后面再重新更新主题内容。当你在这个页面看了一圈终于找到你喜欢的主题之后，点击“Publish”来结束自动生成的过程。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-7-content.png)

一旦你点击了发布（Publish），Github做了所有将游客引流到username.github.io去查看你网站的工作。这可能会花费十分钟左右。一旦这个时间一过，你就可以打开你浏览器的新标签页面去查看你的网站！


制造变化<br>
你要做的第一件事就是将你的首页的标题给移除掉，并且添加一个更为友好的标题。因为这是一个非常迅速的修改，而且是你的第一条修改，所以建议你将这个变化记录到master分支上。

如果你点击了index.html去导航这个文件，你也可以直接点击EDIT来编辑它。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-8-content.png)

让我们找到这个文件中说到的username.github.io然后将其更改为更为友好的标题。对于Octocat而言，我会将其更改为"欢迎来到Octocat的主页！"。你也可以这样做，只不过需要将username改为你的名字。在这个标题下面，你应该添加一条关于这个页面的目的的消息并且用你的话描述。

![](https://obrxbqjbi.qnssl.com/blog/image/gp-9-content.png)

当你做了这个小的改变之后，将鼠标滚动到页面的最下面，然后执行你的第一次提交。你有两个地方去写关于这个变化：一个是主题，另一个是拓展描述。这个拓展描述是可选的，所以，让我们在这个主题里面留一些描述性的信息。

当你一切就绪之后，点击“Commit Changes” 然后你的提交将会在几秒钟生效！

![](https://obrxbqjbi.qnssl.com/blog/image/gp-10-content.png)

下一步<br>
仅仅是因为你在你的项目中做了一些改变是远远不够的！你需要其他的指南知道你如何向其他的项目提交代码。

GitHub Flow <br>
Contributing to Open Source