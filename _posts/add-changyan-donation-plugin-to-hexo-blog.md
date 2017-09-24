---
title: Hexo添加畅言打赏插件
date: 2017-06-27 00:05:28
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/changyan-donation.jpeg
tags:
	- 畅言打赏
	- Hexo主题
categories:
	- 经验感悟
keywords:
	- 畅言打赏
	- Hexo主题
---
![](https://obrxbqjbi.qnssl.com/blog/image/changyan-donation.jpeg)
今天给博客添加了一个打赏功能，是由畅言提供的，感觉还不错的。现在直接说说，如何在Hexo博客中添加打赏功能的插件。

## 获取打赏代码
进入畅言的后台，点击左边的菜单，选择实验室，然后再点击打赏就可以看到打赏的代码了。如下：

![](https://obrxbqjbi.qnssl.com/blog/image/changyan-donation-code.png)

## 配置Hexo博客
和前一篇文章一样，修改`changyan.ejs`和`index.ejs`两个文件就可以了。

### 配置`changyan.ejs`文件

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
    <!-- 代码2：用来读取评论框配置，此代码需放置在代码1之后。 -->
    <!-- 如果当前页面有评论框，代码2请勿放置在评论框代码之前。 -->
    <!-- 如果页面同时使用多个实验室项目，以下代码只需要引入一次，只配置上面的div标签即可 -->
    <script type="text/javascript" charset="utf-8" src="https://changyan.itc.cn/js/lib/jquery.js"></script>
    <script type="text/javascript" charset="utf-8" src="https://changyan.sohu.com/js/changyan.labs.https.js?appid=<%= theme.comment.changyan.appId %>"></script>
<% } else { %>
    <div id="SOHUCS" sid="<%= page.permalink %>"></div>
<% } %>
```

### 修改`index.ejs`文件

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
        <!-- 打赏 -->
        <div style="text-align:center">
        <div id="cyReward" role="cylabs" data-use="reward" sid="<%= page.permalink %>"></div>
        </div>
        <%- partial('comment/changyan') %>
    <% } %>
    </section>
<% } %>
```

## 总结
预览一下结果，直接看下面的“打赏”按钮就行啦，当然，按钮的颜色等信息你可以在畅言的配置中自定义，这个完全看个人喜好。