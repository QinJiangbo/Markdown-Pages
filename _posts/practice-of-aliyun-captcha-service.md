---
title: 阿里云验证码服务实践
date: 2017-11-04 13:38:16
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/aliyun-logo.png
tags:
	- 阿里云
	- 验证码
categories:
	- 开发技术
	- Java
keywords:
	- 阿里云
	- 验证码
---
![阿里云Logo](https://obrxbqjbi.qnssl.com/blog/image/aliyun-logo.png)

在平时上网的时候，我们会见到很多不同的验证码，大多数都是图形验证码，有数字的，有大小写字母的，也有中文的等等。但是这些验证码往往特别容易被OCR技术识别，所以针对人的识别就显得没那么准确了。本文将借力阿里云的验证码服务，介绍一种滑动式的验证码服务实践。

## 传统验证码服务
传统的验证码基本上都类似于下面这种：
![传统验证码](https://obrxbqjbi.qnssl.com/blog/image/tranditional-captcha.jpeg)
它的主要实现方式就是在一个图片上打印出一些文字，然后再添加一些噪声，起一个混淆的作用，主要是用来防止验证码破解服务的。但是借助现有的OCR技术基本上不用太花时间就可以被破解了。所以，使用的价值不大，因此，包括阿里巴巴在内的很多公司决定采用一种新的验证码方式，如下图所示：
![](https://obrxbqjbi.qnssl.com/blog/image/slide-captcha.png)

这种方式相比于传统的验证码服务不容易被OCR识别，但是也有其他的方式去破解，它并不是绝对的安全，但是破解的成本非常高。

## 阿里云验证码服务
这里引用阿里云的同学在云栖社区上发表的一篇文章[验证码对抗之路及现有验证机制介绍](https://yq.aliyun.com/articles/57807)，他分别介绍了阿里云滑动验证码和谷歌reCaptcha的技术体系：
### 阿里云滑动验证码
![](https://obrxbqjbi.qnssl.com/blog/image/aliyun-captcha-architecture.png)

通过基于用户行为的第一道防御将对正常用户的打扰降低，使得正常用户可以以极小的代价通过滑动验证，而同时对不确定的用户实施知识型问题的验证，在拦截机器人的同时，保障业务的正常平稳运行。
### 谷歌reCaptcha
![](https://obrxbqjbi.qnssl.com/blog/image/google-recaptcha-architecture.png)

可以看到，google的模式与阿里的模式有些类似，所不同的是google所使用的验证码模式是点击，而阿里是滑动。

简单分析下google，对于第一步的点击验证，google更多的是通过其基于虚拟机的强混淆器对整个数据采集过程进行了加密，并综合了环境信息（如设备指纹、cookie、点击频率等信息）来进行判断，而第二步的知识验证也包括以下几种（部分在之前的图中没有出现）：

1. 扭曲的图形
2. 图形的分类
3. 高级图形分类(会不断的出图，点完一张又一张)

可以看到二者在架构上是非常相似的，攻击者的成本会非常高！这也就达到我们使用验证码的目的了。

## 代码实践
### 服务申请
首先需要开通阿里云的滑动验证码服务，具体服务如下（这个位置不是太好找QAQ）：

![](https://obrxbqjbi.qnssl.com/blog/image/aliyun-captcha-service.png)

接着拿好自己的**accessKeys**，这个在[https://ak-console.aliyun.com/#/accesskey](https://ak-console.aliyun.com/#/accesskey)可以找到。好了，服务申请准备完毕。

### 项目搭建
关于SpringMVC如何搭建我就不多说了，大家可以看我之前的博客，或者自己到网上查看。主要是两个文件需要编写，一个是jsp页面，另一个是后台对应处理的controller类。我们分别来编写这两个文件：

``` html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>示例-WEB版</title>

    <!-- 此段必须要引入t为小时级别的时间戳 -->
    <link type="text/css" href="//g.alicdn.com/sd/ncpc/nc.css?t=1509677798218" rel="stylesheet"/>
    <script type="text/javascript" src="//g.alicdn.com/sd/ncpc/nc.js?t=1509677798218"></script>
    <!-- 引入结束 -->
</head>
<body>
<!-- 此段必须要引入 -->
<div id="_umfp" style="display:inline;width:1px;height:1px;overflow:hidden"></div>
<!-- 引入结束 -->
<!-- 表单示例，请替换成您的业务表单 -->
<div class="container">
    <form action="/verify" method="post">
        <div class="ln">
            <div id="dom_id"></div>
        </div>
        <input type='hidden' id='csessionid' name='csessionid'/>
        <input type='hidden' id='sig' name='sig'/>
        <input type='hidden' id='token' name='token'/>
        <input type='hidden' id='scene' name='scene'/>
        <div class="ln">
            <input type="submit" value="提交">
        </div>
    </form>
</div>
<!-- 表单示例结束 -->
<!-- 此段必须要引入 -->
<script>
    var nc = new noCaptcha();
    var nc_appkey = 'FFFF0000000001789A78';  // 应用标识,不可更改
    var nc_scene = 'login';  //场景,不可更改
    var nc_token = [nc_appkey, (new Date()).getTime(), Math.random()].join(':');
    var nc_option = {
        renderTo: '#dom_id',//渲染到该DOM ID指定的Div位置
        appkey: nc_appkey,
        scene: nc_scene,
        token: nc_token,
        callback: function (data) {// 校验成功回调
            console.log(data.csessionid);
            console.log(data.sig);
            console.log(nc_token);

            document.getElementById('csessionid').value = data.csessionid;
            document.getElementById('sig').value = data.sig;
            document.getElementById('token').value = nc_token;
            document.getElementById('scene').value = nc_scene;
        }
    };
    nc.init(nc_option);
</script>
<!-- 引入结束 -->
</body>
<!-- 样式示例，请替换成自己的样式 -->
<style>
    body {
        background: #f5f5f5;
        font-size: 14px;
        line-height: 20px;
        margin: 0;
        padding: 0;
    }

    .container {
        background: #fff;
        padding: 20px;
        margin: 20px;
        width: 400px;
    }

    .ln {
        padding: 5px 0;
    }

    .ln .h {
        display: inline-block;
        width: 4em;
    }

    .ln input {
        border: solid 1px #999;
        padding: 5px 8px;
    }
</style>
<!-- 样式示例结束 -->
</html>
```

页面效果如下：

![](https://obrxbqjbi.qnssl.com/blog/image/slide-captcha-validation-page.png)

我们再编写对应的Controller类：

``` java
package com.qinjiangbo.controller;

import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.jaq.model.v20161123.AfsCheckRequest;
import com.aliyuncs.jaq.model.v20161123.AfsCheckResponse;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;
import com.qinjiangbo.util.IpCounter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;

@Controller
public class ValidationController {

    private static Logger logger = LoggerFactory.getLogger(ValidationController.class);

    private static final String ACCESS_KEY = "YOUR_ACCESS_KEY";
    private static final String ACCESS_SECRET = "YOUR_ACCESS_SECRET";
    private static IAcsClient client = null;

    static {
        init();
    }

    /**
     * 初始化验证服务
     */
    private static void init() {
        IClientProfile profile = DefaultProfile.getProfile("cn-hangzhou", ACCESS_KEY, ACCESS_SECRET);
        client = new DefaultAcsClient(profile);
        try {
            DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Jaq", "jaq.aliyuncs.com");
        } catch (ClientException e) {
            logger.error("验证码服务初始化失败！");
        }
    }

    @RequestMapping("/validate")
    public ModelAndView validation(HttpServletRequest request) {
        ModelAndView modelAndView = new ModelAndView("validation");
        return modelAndView;
    }

    @RequestMapping("/verify")
    public ModelAndView verify(HttpServletRequest httpServletRequest) {

        String session = httpServletRequest.getParameter("csessionid");
        String sig = httpServletRequest.getParameter("sig");
        String token = httpServletRequest.getParameter("token");
        String scene = httpServletRequest.getParameter("scene");

        AfsCheckRequest request = new AfsCheckRequest();
        request.setPlatform(3);// 1:Android 2:iOS 3:PC
        request.setSession(session);
        request.setSig(sig);
        request.setToken(token);
        request.setScene(scene);
        
        boolean success = true;
	 
        try {
            AfsCheckResponse response = client.getAcsResponse(request);
            if(response.getErrorCode() == 0 && response.getData() == true) {
                System.out.println("验证通过");
            } else {
            	success = false;
                System.out.println("验证失败");
            }
        } catch (Exception e) {
            logger.error("验证服务出错！");
        }

        ModelAndView modelAndView = new ModelAndView("success");
        modelAndView.addObject("success", success);
        return modelAndView;
    }
}
```
关于成功的页面我就不写了，比较简单，里面就是根据`${success}`的值打印出是成功的还是失败的。

## 总结
现在验证码的识别技术越来越先进，但是验证码的技术也越来越先进；其实我们都可以自己去实现那些传统的验证码，但是传统的验证码已经远远不能满足我们的需求了。这个时候我们就休要借力第三方强大的技术实力支持。我个人比较喜欢阿里云，所以就坚持使用阿里云的服务咯！