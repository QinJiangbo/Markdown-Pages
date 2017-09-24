---
title: Hexo博客添加畅言评论框
date: 2017-06-25 21:49:30
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/changyan.png
tags:
	- 畅言评论框
	- Hexo主题
categories:
	- 经验感悟
keywords:
	- 畅言评论框
	- Hexo主题
---
![](https://obrxbqjbi.qnssl.com/blog/image/changyan.png)

和以前一样，捣鼓捣鼓一件事情成功以后写一篇博客总结一下经验！免得大家和我一样再次踩坑。这一次需要记录的是6月1号（2017-06-01）多说平台关闭事件。以前一直使用多说，后来发了一封邮件，说“由于公司业务调整，多说社会化评论插件将会与6月1号关闭，请知悉！”因此消息一出，我就把多说关闭了，换成了友言，但是后来某一天友言也打不开了，莫名其妙。于是一狠心，就直接将博客的评论关闭了。但是细细想想，写博客是为了什么，其实是记录自己的感悟和总结自己的经验，与大家交流的，关闭评论不等于直接切断了这个通道么？所以决定还是得重新打开评论，但是选择哪一个呢？网易跟帖和搜狐畅言摆在了面前，博主一直都在逛网易，神评论太多，不想把自己的博客搞得像新闻客户端一样，所以选择了畅言。推荐大家也是用这个，功能非常强大！
 
博主使用的是[PPOffice](https://github.com/ppoffice)提供的hueman主题。因此本博客的所有操作针对这个博客有效，大家对照着主题结构修改对应的文件即可。
 
## 畅言工作台
进入畅言的官网[http://changyan.kuaizhan.com/](http://changyan.kuaizhan.com/)，注册一个账号，然后登录进入畅言的工作台，如下：

![](https://obrxbqjbi.qnssl.com/blog/image/changyan-workbench.png) 

看到上面红框标注的内容，是我们待会需要使用到的数据，一个是App ID，一个是App Key。

## 修改主题config.yml文件
在主题文件config.yml中添加如下代码：

``` yml
# Comment
comment:
    disqus: # enter disqus shortname here
    duoshuo: # enter duoshuo shortname here qinjiangbo
    youyan: # 2127917 # enter youyan uid here 2127917
    changyan:
        appId: [APP ID]
        appKey: [APP KEY]
        on: #表示是否开启畅言评论，随便写什么，不为空就行
```

在上面的代码中，我们添加了APP ID和APP KEY两个字段，这两个字段非常重要！

## 创建changyan.ejs文件
在hueman主题的`/hueman/layout/comment/`目录下新建一个`changyan.ejs`文件，照着`disqus.ejs`写一份自己的评论框插件。

``` ejs
<% if (typeof(script) !== 'undefined' && script) { %>
    <script type="text/javascript">
        (function() {
            var appid = '<%= theme.comment.changyan.appId %>';
            var conf = '<%= theme.comment.changyan.appKey %>';
            var width = window.innerWidth || document.documentElement.clientWidth;
            if (width < 960) {
                window.document.write('<script id="changyan_mobile_js" charset="utf-8" type="text/javascript" src="https://changyan.sohu.com/upload/mobile/wap-js/changyan_mobile.js?client_id=' + appid + '&conf=' + conf + '"><\/script>');
            } else {
                var loadJs = function(d, a) {
                    var c = document.getElementsByTagName("head")[0] || document.head || document.documentElement;
                    var b = document.createElement("script");
                    b.setAttribute("type", "text/javascript");
                    b.setAttribute("charset", "UTF-8");
                    b.setAttribute("src", d);
                    if (typeof a === "function") {
                        if (window.attachEvent) {
                            b.onreadystatechange = function() {
                                var e = b.readyState;
                                if (e === "loaded" || e === "complete") {
                                    b.onreadystatechange = null;
                                    a()
                                }
                            }
                        } else {
                            b.onload = a
                        }
                    }
                    c.appendChild(b)
                };
                loadJs("https://changyan.sohu.com/upload/changyan.js", function() {
                    window.changyan.api.config({
                        appid: appid,
                        conf: conf
                    })
                });
            }
        })();
    </script>
<% } else { %>
    <div id="SOHUCS" sid="<%= page.permalink %>"></div>
<% } %>
```

`注意`：**上面第一行代码中`is_home()`这个方法一定要在这个文件里面加上，否则你的博客首页最底下会出现畅言的评论框，这是我们所不希望看到的。**

## 修改scripts.ejs文件
`scripts.ejs`文件在`changyan.ejs`文件的相同目录下，编辑`scripts.ejs`文件并作如下修改：

``` ejs
<% if (theme.comment.disqus) { %>
    <%- partial('comment/disqus', { script: true }) %>
<% } else if (theme.comment.duoshuo) { %>
    <%- partial('comment/duoshuo', { script: true }) %>
<% } else if (theme.comment.youyan) { %>
    <%- partial('comment/youyan', { script: true }) %>
<% } else if (theme.comment.changyan.on) { %>
    <%- partial('comment/changyan', { script: true }) %>
<% } %>
```

## 修改index.ejs文件
`index.ejs`文件也在`changyan.ejs`文件的相同目录下，同样编辑它，

``` ejs
<% if (post.comments) { %>
    <section id="comments">
    <% if (theme.comment.disqus) { %>
        <%- partial('comment/disqus') %>
    <% } else if (theme.comment.duoshuo) { %>
        <%- partial('comment/duoshuo') %>
    <% } else if (theme.comment.youyan) { %>
        <%- partial('comment/youyan') %>
    <% } else if (theme.comment.changyan.on) { %>
        <%- partial('comment/changyan') %>
    <% } %>
    </section>
<% } %>
```

`注意`：**其实一开始是想在这个文件第一行添加`is_post()`方法，但是不奏效，首页依然会显示畅言评论框，所以，没有在这儿添加，如果你知道原因欢迎在下面评论！**

## 总结
畅言是一个非常不错的社会化评论插件平台，提供了非常丰富的功能，支持非常多的平台，唯独没有hexo，是不是很囧。不过大家自己动手搭博客平台的也一定都是动手能力特别强的同学，所以，自己琢磨琢磨就出来啦！

## Bingo
目前将这个插件已经发Pull  Request到官方的Github啦，很高兴已经被采纳了！大家后面可以直接使用这个主题而不需要自己再额外地去找啦！

![Pull Request](https://obrxbqjbi.qnssl.com/blog/image/changyan-PR.png)

`更新`：**这里的`is_post()`方法的问题已经解决！**

![Pull Request](https://obrxbqjbi.qnssl.com/blog/image/changyan-PR2.png)