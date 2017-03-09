---
title: CentOS7安装redis集群指南
date: 2017-02-20 18:04:39
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

## 写在前面
先说一说为什么要写这一篇博客，主要是因为今天在配置redis集群的时候被恶心到了，各种杂七杂八的问题一涌而现，搞得我摸不着北。写这篇博文主要是起一个记录的作用，以免自己日后再遇到此类问题时不知所措，浪费时间。网上的一些博客大多数都写的是单机上的多个实例的情况，这个不符合实际的生产环境要求，所以本文按照完全分布式的情况来部署。本博客的Q&A环节放在下一篇博客里面。现在按照常规的步骤来走一遍。

## 环境准备
博主的操作环境是四台Linux服务器，版本都是CentOS 7 64bit。其中一台是管理机，另外三台是节点机。IP地址分别是，管理机（`192.168.131.140`），节点机1（`192.168.131.141`），节点机2（`192.168.131.142`），节点机3（`192.168.131.143`）。

### 配置安全环境
CentOS 7默认是没有`iptables`服务的，好像变成了一个新的叫`firewalld`的服务。我们还是建议用户使用`iptables`服务，具体通过`yum`来安装：

``` bash
yum -y install iptables-services
```
其中，`-y`表示自动应答，免得安装过程中一直询问是否继续。

先关闭`selinux`安全设置，免得后面添加`iptables`服务失败。

``` bash
vim /etc/selinux/config
```
更改`SELINUX`的值为`disabled`。

``` bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
然后将`iptables`添加到系统启动项并开启它。

``` bash
systemctl enable iptables
systemctl start iptables
```
关于`iptables`是否需要开启存在一些争议，有些人认为需要直接关闭防火墙，这样后面节点之间就可以直接通了，但是出于企业安全的考虑，大多数公司是不会关闭它的，所以需要我们加上这个。当然了，关闭`iptables`的命令很简单（当前不推荐关闭）：

``` bash
systemctl stop iptables
```

### 安装zlib依赖包
redis的安装需要依赖zlib包，所以我们先得安装好zlib包。采用`yum`安装非常简单。

``` bash
yum -y install zlib
```

### 安装ruby运行环境
后面启动整个集群需要使用到ruby编写的脚本，所以有必要先创建好ruby运行环境。一样的使用`yum`安装。

``` bash
yum -y install ruby
```

### 安装redis集群
这里推荐采用源码安装，因为可配置性更高。采用`yum`安装会有很多不方便的地方，比如添加一个新的redis实例。

在每一台机器Linux服务器里面分别使用wget工具从这里[http://download.redis.io/releases/redis-3.2.6.tar.gz](http://download.redis.io/releases/redis-3.2.6.tar.gz)下载好redis的源码包。如果没有wget工具，采用`yum`先安装一个。

``` bash
yum -y install wget
wget http://download.redis.io/releases/redis-3.2.6.tar.gz
```

现在好以后，当前目录就会存在一个`redis-3.2.6.tar.gz`压缩文件。先解压，再安装。建议将其安装到一个`/usr/local/`目录下，因为这个目录是比较通用的一个目录。在里面创建一个文件夹`redis_cluster`。

``` bash
tar -zxvf redis-3.2.6.tar.gz
cd redis-3.2.6
mkdir -p /usr/local/redis_cluster
make install PREFIX /usr/local/redis_cluster
```
安装好了以后在`/usr/local/redis_cluster`目录下就会存在一个生成的`bin`文件夹，里面包含这些文件。`redis-benchmark`  `redis-check-aof`  `redis-check-rdb`  `redis-cli`  `redis-sentinel`  `redis-server`，将之前解压的文件夹`redis-3.2.6`中的`redis.conf`拷贝到`/usr/local/redis_cluster`中。

``` bash
cp redis.conf /usr/local/redis_cluster
```
好这一步安装完成。

### 安装gem-redis插件
安装完ruby之后其实会存在一个rubygem插件管理工具，这里我们需要安装操作redis的工具。很简单，一行命令就搞定了。

``` bash
gem install redis
```

## 设置redis集群
我们的目标是什么？[没有蛀牙]。我们的目标是一个管理机，三个节点机。然后每一个节点机当中起两个redis实例，也就是说，3台节点机，6个实例。

根据前面的步骤，redis已经成功安装完毕了，但是只有一个实例。就是端口为6379的实例。我们分别在三台节点机里面新建两个目录：

``` bash
cd /usr/local/redis_cluster
mkdir -p 6379
mkdir -p 6380
```
并将刚刚安装的`redis_cluster`目录中的文件分别拷贝到文件夹`6379`和`6380`中。

``` bash
cp -r bin 6379
cp redis.conf 6379
cp -r bin 6380
cp redis.conf 6380
rm -rf bin
rm -rf redis.conf
```
`注意`**每一台节点机上都需要进行这个操作。**

### 修改配置文件
由于我们在一台设备上起了两个实例，所以端口不能冲突。文件夹`6379`和`6380`分别设置为他们文件名对应的端口号。因此，进入文件夹`6379`和文件夹`6380`修改`redis.conf`文件的内容是有区别的。

以节点机1（192.168.131.141）的文件夹`6380`为例，修改`redis.conf`中的下列选项：

``` bash
port 6380 # 对应端口
bind 192.168.131.141 # 对应IP地址
daemonize yes
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
```
其他节点机按照这个对应地改就行了，重点是端口和`bind`的IP地址不能搞错。

### 添加iptables端口过滤
由于redis集群内部通讯的时候会使用它的实例端口xxxx和总线端口10000+xxxx。因此，在每一台节点机上面，我们除了需要加上6379和6380两个过滤规则外，还需要添加16379和16380两个过滤规则。

打开`/etc/sysconfig/iptables`按照上面的22号端口的格式来添加：

``` bash
# sample configuration for iptables service
# you can edit this manually or use system-config-firewall
# please do not ask us to add additional ports/services to this default configuration
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 6379 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 6380 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 16379 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 16380 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT 
```
保存退出。重新启动`iptables`。

``` bash
systemctl restart iptables
```

### 启动每一个redis实例
在下面每一个节点机上启动redis实例。

节点机1（192.168.131.141）节点机2（192.168.131.142）节点机3（192.168.131.143）

``` bash
6379/bin/redis-server 6379/redis.conf &
6380/bin/redis-server 6380/redis.conf &
```

### 在管理机上创建redis集群
还记得之前下载的redis-3.2.6文件夹么，打开它，找到里面的`src`文件夹，将`redis-trib.rb`，`redis-server`以及`redis-cli`拷贝到`/usr/local/bin/`下面。

然后直接启动集群

``` bash
redis-trib.rb create --replicas 1 192.168.131.141:6379
192.168.131.141:6380 192.168.131.142:6379
192.168.131.142:6380 192.168.131.143:6379 192.168.131.143:6380
```

可以看到以下信息：

``` bash
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
192.168.131.141:6379
192.168.131.142:6379
192.168.131.143:6379
Adding replica 192.168.131.142:6380 to 192.168.131.141:6379
Adding replica 192.168.131.141:6380 to 192.168.131.142:6379
Adding replica 192.168.131.143:6380 to 192.168.131.143:6379
M: fa18d9e08a38adb66b353a87833a874188604c02 192.168.131.141:6379
   slots:0-5460 (5461 slots) master
S: 919e1155c9f93892cb5484fb44b85f8097f7aaea 192.168.131.141:6380
   replicates f3cbfc4cf2f01041c340dbf6363e7bcfa06c6a1e
M: f3cbfc4cf2f01041c340dbf6363e7bcfa06c6a1e 192.168.131.142:6379
   slots:5461-10922 (5462 slots) master
S: df94fa0b43786db04413cef4effbb948f51e8d95 192.168.131.142:6380
   replicates fa18d9e08a38adb66b353a87833a874188604c02
M: ad4ea1bdfcdeec8e7e5e76fc46b315fcc122629b 192.168.131.143:6379
   slots:10923-16383 (5461 slots) master
S: 744ccd205b0f817fd0346c19225ee3d1dc441c67 192.168.131.143:6380
   replicates ad4ea1bdfcdeec8e7e5e76fc46b315fcc122629b
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
[root@master ~]# redis-trib.rb create --replicas 1 192.168.131.141:6379 192.168.131.141:6380 192.168.131.142:6379 192.168.131.142:6380 192.168.131.143:6379 192.168.131.143:6380
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join.....
>>> Performing Cluster Check (using node 192.168.131.141:6379)
M: fa18d9e08a38adb66b353a87833a874188604c02 192.168.131.141:6379
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
M: f3cbfc4cf2f01041c340dbf6363e7bcfa06c6a1e 192.168.131.142:6379
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: 744ccd205b0f817fd0346c19225ee3d1dc441c67 192.168.131.143:6380
   slots: (0 slots) slave
   replicates ad4ea1bdfcdeec8e7e5e76fc46b315fcc122629b
S: df94fa0b43786db04413cef4effbb948f51e8d95 192.168.131.142:6380
   slots: (0 slots) slave
   replicates fa18d9e08a38adb66b353a87833a874188604c02
M: ad4ea1bdfcdeec8e7e5e76fc46b315fcc122629b 192.168.131.143:6379
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
S: 919e1155c9f93892cb5484fb44b85f8097f7aaea 192.168.131.141:6380
   slots: (0 slots) slave
   replicates f3cbfc4cf2f01041c340dbf6363e7bcfa06c6a1e
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

表示创建成功。

## 总结
本片博客记录了如何安装redis集群，博主踩的坑是将`redis.conf`文件中的`bind`选项设置错误，设置成了`bind 127.0.0.1 192.168.131.141`这样，启动集群的时候每一次都会将结点的监听ip改写为`127.0.0.1`，所以这里需要去掉`127.0.0.1`，不然就要像我这样，找一天了。关于安装过程中可能会出现的各种问题，我们在下一篇博客里讨论。