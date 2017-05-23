---
title: 高性能MySQL之逻辑架构（一）
date: 2017-04-18 20:29:13
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/high-performance-mysql.png
tags:
	- 逻辑架构
	- MySQL
categories:
	- 数据科学
	- MySQL
keywords:
	- 逻辑架构
	- MySQL
---
众所周知，MySQL是一款非常优秀的开源数据库，根据最新的DB-Engines([https://db-engines.com/en/ranking](https://db-engines.com/en/ranking))排名来看，MySQL使用量已经跃居第二了。和Oracle以及Microsoft SQL Server一起稳稳地占据着前三名的绝对地位。不过我们也应该关注另一个发展迅猛的数据库就是PostgreSQL，不过在这个系列里面我们就不聊PostgreSQL啦。

![DB-Engines排名](https://obrxbqjbi.qnssl.com/blog/image/DB-Engines-2017-04.png)

如果你能在大脑中勾画出一个MySQL各个组件之间相互协调工作的架构图，那么说明你已经比较了解MySQL的相关机制了，也更加有利于我们去理解MySQL服务器的工作原理。下面是一个MySQL的逻辑架构图。
![MySQL逻辑架构图](https://obrxbqjbi.qnssl.com/blog/image/mysql-architecture.png)

需要解释一下上面各个组件的作用，可以看到，**最上层的服务并不是MySQL所独有的，大多数基于网络的C/S（客户端/服务端）的工具或者服务都有类似的架构。**比如连接处理，授权认证，安全等。

**第二层架构是MySQL最核心的部分。**大多数MySQL的核心功能都在这里实现，比如查询解析、分析、优化、缓存以及所有的内置函数（built-in functions）（比如，日期，时间，数学和加密函数），所有跨存储引擎的功能都在这一层实现：存储过程、触发器、视图等。

**第三层是存储引擎。**MySQL的存储引擎有很多，其中最出名的存储引擎有两种：Innodb和MyISAM。存储引擎负责MySQL中数据的存储与提取。和GNU/Linux下面的文件系统一样，每个存储引擎都有它自己的优势和劣势。服务器通过API与存储引擎进行通信。这些接口屏蔽了不同存储引擎的具体实现上的差异。使得这些差异对于上层的查询是透明的。存储过程API包含几十个底层函数，用于执行诸如“开始一个事务”或者“根据主键提取一行数据”等操作。但是存储引擎不回去解析SQL（但是Innodb是一个例外，它会解析外键定义，因为MySQL本身并没有实现这个功能），不同的存储引擎之间也不会相互通信，而只是简单地响应上层的请求。

`参考文献`：《高性能MySQL》