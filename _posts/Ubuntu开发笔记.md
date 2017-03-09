---
title: Ubuntu开发笔记
date: 2016-04-11 23:07:11
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/ubuntu2-thumbnail.jpg
tags:
	- Linux
	- 开发经验
categories:
	- 架构师
	- Linux
keywords:
	- Linux
	- 开发经验
---

# 安装JDK
解压文件，文件夹为jdk 1.8.0_20，并且sudo gedit /etc/environment

在后面加入以下几行：

``` bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/lib/jdk1.7.0/bin"  
CLASSPATH=.:/usr/local/lib/jdk1.7.0/lib  
JAVA_HOME=/usr/local/lib/jdk1.7.0  
```

安装mysql命令
sudo apt-get install mysql-server 

安装图形化界面
sudo apt-get install mysql-admin

启动服务
sudo service mysql start

关闭服务
sudo service mysql stop

安装mysql workbench
(先进入包所在目录)

sudo dpkg -i ./mysql-workbench-community-6.1.7-1ubu1404-amd64.deb sql

接下来会报一系列依赖错误，一个一个去安装缺省的包
sudo apt-get install [package_name]

# 安装Tomcat
只需要在环境变量里面增加tomcat的bin路径

``` bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/richard/jdk1.8.0_20/bin:/home/richard/apache-tomcat-8.0.12/bin"
CLASSPATH="/home/richard/jdk1.8.0_20/lib"
JAVA_HOME="/home/richard/jdk1.8.0_20"
```

注意，后面如果报错，就在catalin.sh文件第一行加上JAVA_HOME="/home/richard/jdk1.8.0_20"


# 安装PHP
php的安装要注意:

1. sudo gedit /etc/apache2/sites-enabled/000-default.conf
2. 修改DocumentRoot为/var/www/（删掉默认的/html/）


apache2配置的时候要注意以后服务器名会变，所以加上

``` bash
# Server Name
ServerName 127.0.0.1
```

## apache2默认路径更改

cd /etc/apache2/sites-enabled/
sudo gedit 000-default.conf
修改一下DocumentRoot即可

``` bash
# DocumentRoot /var/www/
DocumentRoot /home/richard/workspace/
<Directory /home/richard/workspace/>  
Options Indexes FollowSymLinks Multiviews  
AllowOverride all  
Order allow,deny  
Allow from all
</Directory>
```

注意！！！需要修改workspace目录的读写权限

### sudo chmod 777 -R /home/richard/workspace

php5安装以后会改变默认权限
cd /etc/apache2/
sudo gedit apache2.conf
修改以下内容

``` bash
<Directory />
Options FollowSymLinks
AllowOverride None
Require all granted<!-- 这个默认是deny,会造成403,permission denied -->
</Directory>
```

# Apache2服务器重启
sudo /etc/init.d/apache2 restart

管理工具phpmyadmin404解决方法
Include /etc/phpmyadmin/apache.conf
(目录同上述的文件目录一样)

tomcat设置开机启动
cd /etc
sudo gedit rc.local

添加startup.sh的路径即可
cd /home/richard/apache-tomcat-8.0.12/bin && ./startup.sh


# 配置JAVA环境 

sudo gvim /etc/environment  

打开环境配置文件。对了，这一步各们不一定要用 gvim 去打开，因为你不一定装了gvim，用 gedit, vi 等打开也一样。不熟悉 vim 的请使用 gedit 
打开后可以看到: 

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games"  

一行文字，你们的可能跟我的有些出入。将其修改为： 

``` bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/lib/jdk1.7.0/bin"  
CLASSPATH=.:/usr/local/lib/jdk1.7.0/lib  
JAVA_HOME=/usr/local/lib/jdk1.7.0  
```

其中的jvm/jdk1.7.0等请跟据自己下载的安装包和安装目录对号修改。然后重新加载.bashrc
    source ~/.bashrc  


5、到这里后运行 javac, java, 等命令还是不能用，和 windows 安装配置后就可以用不一样。接下来再执行下面命令： 

``` bash
sudo update-alternatives --install /usr/bin/java java /home/richard/Softwares/jdk1.8.0_25/bin/java 300  
sudo update-alternatives --install /usr/bin/javac javac /home/richard/Softwares/jdk1.8.0_25/bin/javac 300  
sudo update-alternatives --install /usr/bin/javap javap /home/richard/Softwares/jdk1.8.0_25/bin/javap 300  
sudo update-alternatives --install /usr/bin/javadoc javadoc /home/richard/Softwares/jdk1.8.0_25/bin/javadoc 300 
```

6,sudo gedit /etc/profile

``` bash
export JAVA_HOME=/home/richard/Softwares/jdk1.8.0_25
export PATH=PATH:JAVA_HOME/bin:JAVA_HOME/jre/bin
export CLASSPATH=.:JAVA_HOME/lib/dt.jar: JAVA_HOME/lib/tools.jar
```

# LAMP+phpmyadmin安装教程
## 一.安装
### 1.安装LAMP
在新立得软件包管理器中选择 编辑－－使用任务分组标记软件包

在打开的窗口中 勾选 LAMP SERVER 然后确定。

在主窗口中 点击绿色的对号 应用 按钮

好了 。接下来就是等待…等待新立得 自动下载安装完。

中间会有一次提示输入mysql的root用户的密码

您还可以在终端模式下，通过命令行安装：
sudo apt-get install apache2 libapache2-mod-php5 php5 php5-gd mysql-server php5-mysql phpmyadmin
### 2.安装phpmyadmin
终端中运行命令
sudo apt-get install phpmyadmin
## 二.配置
1> apache 的配置文件路径 /etc/apache2/apache2.conf
2> php.ini 路径 /etc/php5/apache2/php5.ini
3> mysql配置文件 路径 /etc/mysql/my.cnf
4> phpmyadmin配置文件路径 /etc/phpmyadmin/apache.conf
5> 网站根目录 /var/www
### 1. 配置apache
终端中 使用命令
sudo gedit /etc/apache2/apache2.conf
在配置文件最后面加入下面几行：
添加文件类型支持
AddType application/x-httpd-php .php .htm .html
默认字符集 根据自己需要
AddDefaultCharset UTF-8
服务器地址
ServerName 127.0.0.1
添加首页文件 三个的顺序可以换 前面的访问优先 （当然你也可以加别的 比如default.php）
DirectoryIndex index.htm index.html index.php
### 2.配置PHP5
这个没什么好说的 根据个人自己需要
下面是默认时区
;default.timezone=去掉前面的分号 后面加个PRC 。表示中华人民共和国（就是GMT＋8时区）
default.timezone= PRC
### 3.配置mysql
sudo gedit /etc/mysql/my.cnf
这里有一个地方要注意

因为默认是只允许本地访问数据库的 如果你有需要 可以打开。

bind-address 127.0.0.1这一句是限制只能本地访问mysql的。如果有需要其他机器访问 把这句话用\#注释掉

\# bind-address 127.0.0.1
### 4.配置phpmyadmin
phpmyadmin 默认并不是安装在 /var/www下面的而是在 /usr/share/phpmyadmin

你可以把phpmyadmin复制过去 或者 网上有人说你可以创建一个链接 然后把链接复制过去（没有试过）

然后 终端中运行命令

sudo gedit /etc/phpmyadmin/apache.conf

然后把下面两句的路径 改为/var/www/phpmyadmin

Alias /phpmyadmin /usr/share/phpmyadmin

改为：

Alias /phpmyadmin /var/www/phpmyadmin

### 符：常用命令

1. 重启apache
sudo /etc/init.d/apache2 restart
2. 重启mysql
sudo /etc/init.d/mysql restart
至此 LAMP环境配置成功,试一下 echo phpinfo(); 吧！
3. LAMP并没有那么神秘！除去下载的时间，整个配置过程决不会花费您五分钟。
4. GD库的安装
5. sudo apt-get install php5-gd
    记得装完重启apache
6. sudo /etc/init.d/apache2 restart
7. 启用 mod_rewrite 模块
    sudo a2enmod rewrite


# Sublime text 2 安装
ubuntu 12.04 sublime text 2 破解

1. 将sublime_text文件复制一份并重命名为sublime_text.bak做一个备份

2. 用sublime text 2打开sublime_text.bak文件，搜索 3342 并替换为 3242(所有都替换)， 然后保存

3. 关闭 sublime text 2应用程序， 并删除 sublime_text 文件, 把备份文件sublime_text.bak修改为 sublime_text.bak

4. 输入序列号


打开sublime_text，点击help->enter lisence key，在弹出对话框粘帖以下内容：（从——BEGIN到LICENSE——）

``` java
—–BEGIN LICENSE—–
USA
Unlimited User License
EA7E-1640
763D05839CA08BDA7B0103B5BABF0150
195EE53CC33B569858AFD553F080A9BC
1F678C88A1342AC92CA596FE775E7014
5A0EE55DC2F8DE3C4ED6B5B02FD4DB3C
493FCE3EE61FC0588CDAFAAD731BB47F
FD047777D02A5BE92202B3D3EB59A696
A69DFEF6687D16FCD4443556912A1F62
82DA125263C5BC270CEE7664B5D0CEB9
—–END LICENSE—–
```

# Ubuntu Nginx 安装及其配置
## Nginx安装

### 3.1 安装Nginx


1. 在线安装

sudo apt-get install nginx

Nginx的版本是1.2.1

ubuntu安装Nginx之后的文件结构大致为：

所有的配置文件都在/etc/nginx下，并且每个虚拟主机已经安排在了/etc/nginx/sites-available下

启动程序文件在/usr/sbin/nginx

日志放在了/var/log/nginx中，分别是access.log和error.log

并已经在/etc/init.d/下创建了启动脚本nginx

默认的虚拟主机的目录设置在了/usr/share/nginx/www


2. 源代码安装


下载地址：http://nginx.org/download/


我这里下载的是 nginx-1.3.9.tar.gz，安装过程很简单，如下：

``` bash
./configure
make
make install
```

安装成功之后，nginx放置在/usr/local/nginx目录下，主要的配置文件为conf目录下的nginx.conf，

nginx的启动文件在sbin目录下的nginx文件。


### 3.2 启动Nginx


1. 在线安装的启动过程
sudo /etc/init.d/nginx start

2. 源代码安装的启动过程

``` bash
cd /usr/local/nginx
sbin/nginx
```

然后就可以访问了，http://localhost/ ， 一切正常！如果不能访问，先不要继续，看看是什么原因，解决之后再继续。

如果你的机器同时安装了Apache，那上面的访问方式就不能使用了，而且nginx都可能启动不了，这是
因为它们都是用了80这个端口。我们这里将nginx的端口修改为8080，
这里主要修改nginx的配置文件nginx.conf，将一下这一行


listen 80;

修改为

listen 8080;

然后就可以访问了，http://localhost:8080/ 。


### 3.3 安装PHP和MySQL
sudo apt-get install php5-cli php5-cgi mysql-server php5-mysql


### 3.4 测试Nginx对PHP的支持


(1). 重新启动nginx:

/etc/init.d/nginx restart

(2). 启动FastCGI:

``` bash
spawn-fcgi -a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi
spawn-fcgi启动出现错误时，查看php-cgi是否安装，如果么有的话，安装php5-cgi。
```

sudo apt-get install php5-cgi

(3). 测试

打开http://localhost/phpinfo.php


(4). Nginx配置

Nginx的配置文件是/etc/nginx/nginx.conf，其中设置了一些必要的参数，我们发现其中这样的语句：

include /etc/nginx/sites-enabled/*

可以看出/etc/nginx/sites-enabled/default文件也是一个核心的配置文件，其中包含了主要的配置信息，

如服务器跟目录、服务器名称、location信息和server信息。

对于源代码安装的nginx，配置文件为/usr/local/nginx/conf/nginx.conf。

下面主要说明location的匹配规则：

1. = 前缀的指令严格匹配这个查询。如果找到，停止搜索。

2. 剩下的常规字符串，最长的匹配优先使用。如果这个匹配使用 ^~ 前缀，搜索停止。

3. 正则表达式，按配置文件里的顺序，第一个匹配的被使用。

4. 如果第三步产生匹配，则使用这个结果。否则使用第二步的匹配结果。

在location中可以使用常规字符串和正则表达式。

如果使用正则表达式，你必须使用以下规则：

（1）~* 前缀选择不区分大小写的匹配

（2）~  选择区分大小写的匹配


例子：

``` nginxconf
location = / {


　　　　\# 只匹配 / 查询。


　　　　[ configuration A ]
}


location / {

　　　　\# 匹配任何查询，因为所有请求都以 / 开头。

              \# 但是正则表达式规则和长的块规则将被优先和查询匹配。

　　　　[ configuration B ]

}


　　location ^~ /images/ {


　　　　\# 匹配任何以 /images/ 开头的任何查询并且停止搜索。


\# 任何正则表达式将不会被测试。


　　　　[ configuration C ]


　　}


　　location ~* \.(gif|jpg|jpeg) {


\# 匹配任何以 gif、jpg 或 jpeg 结尾的请求。

\# 然而所有 /images/ 目录的请求将使用 Configuration C。

　　　[ configuration D ]

　　}
```

这里你还要对正则表达式有一定的了解！！！

# PDO常用方法及其应用
- PDO::query() 主要是用于有记录结果返回的操作，特别是SELECT操作
- PDO::exec() 主要是针对没有结果集合返回的操作，如INSERT、UPDATE等操作
- PDO::lastInsertId() 返回上次插入操作，主键列类型是自增的最后的自增ID
- PDOStatement::fetch() 是用来获取一条记录
- PDOStatement::fetchAll() 是获取所有记录集到一个中 
