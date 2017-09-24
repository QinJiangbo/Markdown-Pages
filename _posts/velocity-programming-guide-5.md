---
title: Velocity编程指南（五）
date: 2017-07-02 18:01:35
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/velocity-thumbnail.png
tags:
	- Velocity
	- 模板引擎
categories:
	- 开发技术
	- Java
keywords:
	- Velocity
	- 模板引擎
---
本章作为这个系列的最后一章，主要是讲如何在Java代码中运行Velocity模板引擎，还是一贯的套路，使用Maven项目来演示，主要从以下几个方面来讲：

+ pom依赖
+ 程序演示
+ 结果展示
+ 注意事项
+ 总结

## pom依赖
在网站[http://www.mvnrepository.com](http://www.mvnrepository.com)中搜索**“velocity”**，就可以得到这个依赖：

``` xml
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
</dependency>
```
这样相应的Jar包就会自动下载了。

## 程序演示
新建一个Java类`VelocityDemo`，如下：

**VelocityDemo.java**

``` java
import org.apache.velocity.Template;
import org.apache.velocity.VelocityContext;
import org.apache.velocity.app.Velocity;
import org.apache.velocity.app.VelocityEngine;

import java.io.StringWriter;

/**
 * @date: 22/06/2017 12:46 AM
 * @author: qinjiangbo@github.io
 */
public class VelocityDemo {

    public static void main(String[] args) {

        VelocityEngine velocityEngine = new VelocityEngine();
        String path = "/Users/Richard/IdeaProjects/Velocity/src/main/resources";

        velocityEngine.setProperty(Velocity.FILE_RESOURCE_LOADER_PATH, path);
        velocityEngine.setProperty(Velocity.INPUT_ENCODING, "UTF-8");
        velocityEngine.setProperty(Velocity.OUTPUT_ENCODING, "UTF-8");

        velocityEngine.init();

        Template template = velocityEngine.getTemplate("alibaba.vm");
        VelocityContext context = new VelocityContext();

        StringWriter writer = new StringWriter();
        template.merge(context, writer);

        System.out.println(writer.toString());

    }
}

```

**alibaba.vm**

``` vm
#set($we = "Alibaba")
This is an ${we} day.
```

## 结果展示
整个程序运行以后的结果如下所示：

``` bash
This is an Alibaba day.
```

## 注意事项
上面的代码中需要特别关注的一点就是Velocity引擎对象`velocityEngine`的属性值设置，如果不设置`Velocity.FILE_RESOURCE_LOADER_PATH`这个值，Velocity引擎对象根本找不到对应的工作空间，也就无法识别这个`alibaba.vm`模板了。很多教程给出Java例子的时候都没有给出这个值的设置情况。

## 总结
终章结束。希望大家结合本教程的例子，动手实际地在IDE里面跑一遍，这样很多问题才会暴露出来，才会得以解决。有任何问题可以给我留言，可能的话我会在第一时间给予回复。