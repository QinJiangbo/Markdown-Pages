---
title: MySQL导入大文件调参
date: 2017-11-04 14:58:52
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/mysql-logo.png
tags:
	- 导入数据
	- 大文件
categories:
	- 数据科学
	- MySQL
keywords:
	- 导入数据
	- 大文件
---
今天利用MySQL导入数据的时候发生了一个错误，报错的信息是**“MySQL server has gone away”**。这个如何解决呢？我先说说我的工具，我导入数据使用的是Navicat Premium 12.0.13，然后MySQL使用的是Homebrew安装的。

写这篇文章目的主要是记录一下这个问题的解决方案，以免以后重蹈覆辙。其实答案很简单，就是调整MySQL一个配置项`max_allowed_packet`，我设置的是768M。Homebrew安装默认MySQL的配置文件在`/usr/local/etc`这个目录下。我们可以打开这个文件进行编辑：

### my.cnf
``` sh
# Default Homebrew MySQL server config
[mysqld]
# Only allow connections from localhost
bind-address = 127.0.0.1
max_allowed_packet = 768M
```

重启MySQL服务器：

``` sh
mysql.server restart
```

再次导入的时候就可以啦！

## 总结
这个问题其实可能还有其他的原因，有人会说有可能是因为超时，所以也需要改一下`wait_timeout=2880000 interactive_timeout = 2880000`这两个变量。具体的情况需要具体分析，不过也提供了一种可能的思路。最常见的原因就是本文所说的情况啦，基本上这么一改就会OK的。