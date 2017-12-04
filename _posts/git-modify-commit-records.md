---
title: git修改全部提交记录
date: 2017-11-21 17:18:00
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/git-thumbnail.jpg
tags:
	- Git
	- 提交记录
categories:
	- 架构师
keywords:
	- Git
	- 提交记录
---
![](https://obrxbqjbi.qnssl.com/blog/image/git-thumbnail.jpg)

最近使用Intellij IDEA进行开发的时候需要添加一个版本控制，不知道为何提交到Gitlab私库的时候用户名称全部显示为了Github上面的用户名称和邮箱。通过`git log`命令一查看全部提交都是这样的。因此需要找到一种能够修改上次或者全部提交记录的方式。对于Git的高级用法还没有系统研究过，所以，就上网查询了一下，发现`git filter-branch`真心不错，可以对所有的记录进行更正。


## 修改全部记录
具体使用方式如下，在目标目录下执行这条命令即可：

``` sh
$ git filter-branch -f --env-filter \
"GIT_AUTHOR_NAME='[YOUR_NAME]'; \
GIT_AUTHOR_EMAIL='[YOUR_EMAIL]'; \
GIT_COMMITTER_NAME='[YOUR_NAME]'; \
GIT_COMMITTER_EMAIL='[YOUR_EMAIL]';" HEAD
```
可以看到会出现以下输出：

``` sh
Rewrite 3368f52b1c24a8b9fcac3864089c08bfe0929d4d (1/7) (0 seconds passed, remaining 0 predicted) 
Rewrite 0a1444fab2235a67fb94a3f60ea3b8350c6c4b04 (2/7) (0 seconds passed, remaining 0 predicted) 
Rewrite 1fe88255450e24fac784d23530f0b463496d0ef7 (3/7) (0 seconds passed, remaining 0 predicted) 
Rewrite 577de20132b50faa4c874134973d5d2b8bb8ad2a (4/7) (0 seconds passed, remaining 0 predicted) 
Rewrite 931f04cb491427e5a504751ab1dc4bec4e059050 (5/7) (0 seconds passed, remaining 0 predicted) 
Rewrite bceaa47e80d55e2b8647ff2d847c7cd852c162ce (6/7) (0 seconds passed, remaining 0 predicted) 
Rewrite a0790a0780e70d62143287bf10f09783b00d4a00 (7/7) (0 seconds passed, remaining 0 predicted) 
Ref 'refs/heads/master' was rewritten
```

但是如果之前的这些变更我们已经提交到私库里面去怎么办呢？很简单，我们接下来使用`git push -f`命令强制让私库修改这些变更。

``` sh
$ git push -f
Counting objects: 167, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (152/152), done.
Writing objects: 100% (167/167), 258.59 KiB | 3.15 MiB/s, done.
Total 167 (delta 62), reused 0 (delta 0)
remote: Resolving deltas: 100% (62/62), done.
To https://gitlab.com/anti-crawler-system/DemoProject.git
 + a0790a0...52621a8 master -> master (forced update)
```
接着去私库中查看就会发现已经完全修改好啦！

## 修改上一次记录
那修改上一次的提交记录呢？使用什么命令？答案是`git commit --amend`命令，具体使用方式如下：

``` sh
git commit --amend --author="NewAuthor <NewEmail@address.com>"
```

## 总结
关于Git的用法有很多，其实遇到我这种需要修改全部提交记录的情况并不常见，但是需要提前先了解一下，不然问题出现的时候都无所适从。修改提交者记录的原因有很多，但不管怎么样，使用这两个方法都是可以的。如果是需要合并提交记录的话可以使用`git rebase`命令，具体用法后面会给大家介绍的。
