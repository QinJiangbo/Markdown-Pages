---
title: CentOS7安装redis集群QA
date: 2017-02-20 18:06:31
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/redis.png
tags:
	- Hadoop
	- Linux
categories:
	- 架构师
	- Linux
keywords:
	- Hadoop
	- Linux
---
![](https://obrxbqjbi.qnssl.com/blog/image/redis.png)

前一篇博客讲了如何来安装redis集群，那么本片博客主要讨论可能出现的各种问题。

## issue1: ERR Invalid node address specified
启动集群的时候如果使用`hostname`的话会报这个错误。这是由于`redis-trib.rb`对域名或主机名支持不好，故在创建集群的时候要使用`ip:port`的方式。

``` bash
redis-trib.rb create ip1:port1 ip2:port2 ip3:port3
```

## issue2: ERR slot 0 is already busy (redis::commanderror)
这是因为上一次失败的配置文件没有被删除，所以会导致这个问题的存在。这里推荐大家将redis目录下的`appendonly.aof`，`dump.rdb`以及`nodes-6379.conf`等文件全部删除。

## issue3: Waiting for the cluster to join........
无限的等待，博主之前就是这个问题耽误了好久。这个问题主要还是管理机和节点机之间没有连接成功。原因两种：

1. 防火墙屏蔽，可以在防火墙中加入相关的端口, xxxx以及xxxx+10000。
2. redis.conf中`bind`设置更改一下，改为它的真实地址，比如局域网地址`192.168.131.141`而不要使用内部环路地址`127.0.0.1`。

## issue4: [ERR] Node XXXXXX is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0
这个也是因为上一次失败的配置文件没有被删除，所以会导致这个问题的存在。推荐大家将redis目录下的`appendonly.aof`，`dump.rdb`以及`nodes-6379.conf`等文件全部删除。

### 好了，目前就这四个问题，后面如果还有新的问题我再更新！