---
title: 利用七牛云给Hexo博客加速
date: 2016-11-04 09:10:13
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/qiniu-logo.png
tags:
	- 七牛云
	- 静态资源
categories:
	- 经验感悟
keywords:
	- 七牛云
	- 静态资源
---
## 本文背景
昨天夜里本来想写一篇设计模式的博文，先去查看了一下博客，忽然发现博客系统的css和js样式全部失效了。通过默认的访问方式全部都没有用，整个博客的用户体验非常差。然后我就开始找原因，因为采用的是阿里云CDN加速的，又是在深夜，我在想是不是阿里云官方在升级CDN系统导致我的全站样式文件全部失效，后来发现过了俩小时还不行，那就不是阿里云的问题了。然后一直找到3点钟还没有头绪，就睡了一觉，睡的时候还在想如何加载这个css和js的问题。

到了今天早上终于有头绪了，因为发现默认`访问路径是当前路径加上当前系统根路径`。那既然我的默认的访问方式不支持，那为什么不把相对路径改为绝对路径，这个时候我又想起了远方的朋友--七牛云！

## 问题详述
首先说一下我的博客系统，采用**hexo 3.0+版本**，主题采用的是**PPOffice的Hueman**。因为接下来的变化和修改都是基于这个主题来的，但是大家也不要担心，其实别的主题结构也一样，对照着改就行了。我们可以看到执行`hexo g`命令以后根路径下面会生成`public`文件夹，这个文件夹里面有我们需要的样式文件，是从`hueman`主题文件夹下的`source`文件夹拷贝过来的。

![](https://obrxbqjbi.qnssl.com/blog/image/hexo-dir01.png)

## 解决方案
我们下面的任务就是将上图中的`css，js和vendor`三个文件夹全部上传至七牛云，怎么做呢？七牛云为我们提供了了官方的sdk支持，这下就方便多了。先贴一下脚本代码：

``` py
# coding=utf-8

"""
   Date: 04/11/2016
   Author: qinjiangbo@github.io
 
"""
from qiniu import Auth, put_file
import os
import re

access_key = '/YOUR/ACCESS/KEY/HERE'
secret_key = '/YOUR/SECRET/KEY/HERE'

q = Auth(access_key, secret_key)
bucket_name = 'static'

# 根路径(对应修改为你博客的根路径)
blog = '/Users/Richard/Github/qinjiangbo.github.io/'

#public文件夹
vendor_dir = 'public/vendor'
css_dir = 'public/css'
js_dir = 'public/js'


def list_file(file_path):
    for path in os.listdir(file_path):
        full_path = os.path.join(file_path, path)
        if os.path.isfile(full_path):
            key = re.sub(r".*?public/", "", full_path)
            token = q.upload_token(bucket_name, key, 3600)
            ret, info = put_file(token, key, full_path)
            print(ret)
            print(info)
        if os.path.isdir(full_path):
            list_file(full_path)


list_file(blog + vendor_dir)
list_file(blog + css_dir)
list_file(blog + js_dir)
```

等一下，先别着急运行代码，因为没有导入七牛的SDK一定会报错！！！具体有以下几步需要做：

- 首先到七牛的官网下载SDK，如果你还没注册七牛云的话需要先注册，然后进入开发者中心。如下

![](https://obrxbqjbi.qnssl.com/blog/image/hexo-dir02.png)

- 其次进入你的控制台，到个人面板里面找到密钥管理-->访问密钥(AK)和授权密钥(SK)。复制

![](https://obrxbqjbi.qnssl.com/blog/image/hexo-dir03.png)

- 到上面的代码中相应的位置填上。

在PyCharm或者Eclipse里面运行这段代码，然后你的`js，css以及vendor`三个文件夹下面的所有的文件就全部上传至七牛云了。

下面一步很关键，`修改相对路径为绝对路径`

修改hueman主题的配置文件`.config.yml`，添加一个路径`cdn_prefix`，这个路径是你的七牛云的对象存储里面CDN加速的路径，你可以点击https随机生成一个，或者是添加自定义的域名，博主建议你选择前者。

``` yml
# cdn
cdn_prefix: https://og3d9wq14.qnssl.com/
```

进入这个目录`/Users/Richard/GitHub/qinjiangbo.github.io/themes/hueman/layout`，修改为你对应的就行，可以看到下面这些文件

![](https://obrxbqjbi.qnssl.com/blog/image/hexo-dir04.png)

你需要更改的文件夹`common`和`plugin`，先进入`common`，找到`head.ejs`，修改如下代码

![](https://obrxbqjbi.qnssl.com/blog/image/hexo-dir05.png)

`plugin`下面的`scripts.ejs`也是一样的，就不赘述了。读者朋友们可以根据自己的实际情况去查看自己博客中对应的文件，不一定非得和博主一样，只要是涉及到前面说的`jss, css和vendor`三个文件夹下面的脚本文件的，全部都这么处理。后面记得把`layout`文件夹下面的`.DS_Store`文件删除（如果有的话，不然`hexo g`会报错）

好了，基本上大功告成！运行`hexo d`发布博客，就可以看效果了！

## 总结
出现问题不可怕，一定会解决的！那还担心啥！心态调整好，该睡觉睡觉，该思考思考！换个角度想问题可能会更好！