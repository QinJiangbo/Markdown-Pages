---
title: 使用Maven生成Fat Jar
date: 2017-06-11 23:43:05
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/maven-thumbnail.png
tags:
	- maven
	- fat jar
categories:
	- 开发技术
	- Java
keywords:
	- maven
	- fat jar
---
在这篇博客里面我们将详细地讲解如何利用maven来创造一个`fat jar`。可能大家对于`fat jar`的概念还不是很熟悉，没关系，在开始进一步的讲解之前我会跟你说明`fat jar`是什么的。另外本文的环境是

``` java
Maven 3.3.9
JDK 1.8
Joda-Time 2.5
```
大家根据相应的情况自己创建一个Maven项目。

## 什么是Fat Jar?
什么是`Fat Jar`？简单地说就是`胖Jar`呗！哈哈！就是说这个Jar所装的东西比一般的Jar要多嘛！一般地，我们通过maven的插件`maven-jar-plugin`所打包生成的jar都是只包含我们项目的源码的，它不包含我们所依赖的第三方库，这样就会导致一个问题，如果我们的第三方库不在`CLASSPATH`下面会怎样？当然是会出现`java.lang.NoClassDefFoundError`的问题咯！因此，我们需要使用一种方式，能使得项目所依赖的第三方库能够随着我们的项目源码一起被打包，这样我们就完全不用担心类找不到的问题啦！这就是`Fat Jar`的来历！

## 创建Maven项目
两种方式：通过命令行或者通过IDE生成。

(1). 通过命令行：

``` bash
$ mvn archetype:generate -DgroupId=com.qinjiangbo -DartifactId=FatJar
 -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

(2). 通过IDE生成，这里我用的IDEA
![](https://obrxbqjbi.qnssl.com/blog/image/IDE-Maven-Builder.png)

## 项目视图和主类
![](https://obrxbqjbi.qnssl.com/blog/image/Project-View.png)

## 生成普通Jar
生成普通Jar我们可以使用`maven-jar-plugin`这个插件来打包。Maven配置代码如下：

``` xml
<build>
    <finalName>fat-jar-example</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>com.qinjiangbo.App</mainClass>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
    </plugins>
</build>
```
其中`<mainClass>com.qinjiangbo.App</mainClass>`就是指明了main函数的所在地。我们生成好的jar名为`fat-jar-example.jar`，在命令行打开项目的`target`目录：

``` bash
# Richard at Richards-MacBook-Pro.local in ~/IdeaProjects/FatJar/target [23:28:14]
→ jar tf fat-jar-example.jar 
META-INF/
META-INF/MANIFEST.MF
com/
com/qinjiangbo/
com/qinjiangbo/App.class
META-INF/maven/
META-INF/maven/com.qinjiangbo/
META-INF/maven/com.qinjiangbo/FatJar/
META-INF/maven/com.qinjiangbo/FatJar/pom.xml
META-INF/maven/com.qinjiangbo/FatJar/pom.properties
```
很明显，这个jar包里面没有第三方库。我们来看看执行结果是什么？

``` bash
# Richard at Richards-MacBook-Pro.local in ~/IdeaProjects/FatJar/target [23:28:21]
→ java -jar fat-jar-example.jar 
Hello Fat Jar!
Exception in thread "main" java.lang.NoClassDefFoundError: org/joda/time/LocalDate
	at com.qinjiangbo.App.main(App.java:12)
Caused by: java.lang.ClassNotFoundException: org.joda.time.LocalDate
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 1 more
```
可以看到，这个Jar 正常地执行了一半，执行`LocalDate.now()`的时候报错了，因为找不到`LocalDate`这个类，很好理解了吧？好，我们再看看Fat Jar是什么样子的。

## 生成Fat Jar
生成Fat Jar我们使用的是One-Jar这个Maven插件。具体配置代码如下：

``` xml
<build>
    <finalName>fat-jar-example</finalName>
    <plugins>
        <plugin>
            <groupId>com.jolira</groupId>
            <artifactId>onejar-maven-plugin</artifactId>
            <version>1.4.4</version>
            <executions>
                <execution>
                    <goals>
                        <goal>one-jar</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

通过`mvn package`生成的Jar名称为`fat-jar-example.one-jar.jar`。同样的，找到项目`target`所在的目录：首先看看Fat Jar里面究竟是什么？

``` bash
# Richard at Richards-MacBook-Pro.local in ~/IdeaProjects/FatJar/target [23:41:12]
→ jar -tf fat-jar-example.one-jar.jar 
META-INF/MANIFEST.MF
main/fat-jar-example.jar
lib/joda-time-2.5.jar
com/
com/simontuffs/
com/simontuffs/onejar/
.version
OneJar.class
com/simontuffs/onejar/Boot$1.class
com/simontuffs/onejar/Boot$2.class
com/simontuffs/onejar/Boot$3.class
com/simontuffs/onejar/Boot.class
com/simontuffs/onejar/Handler$1.class
com/simontuffs/onejar/Handler.class
com/simontuffs/onejar/IProperties.class
com/simontuffs/onejar/JarClassLoader$1.class
com/simontuffs/onejar/JarClassLoader$2.class
com/simontuffs/onejar/JarClassLoader$ByteCode.class
com/simontuffs/onejar/JarClassLoader$FileURLFactory$1.class
com/simontuffs/onejar/JarClassLoader$FileURLFactory.class
com/simontuffs/onejar/JarClassLoader$IURLFactory.class
com/simontuffs/onejar/JarClassLoader$OneJarURLFactory.class
com/simontuffs/onejar/JarClassLoader.class
com/simontuffs/onejar/OneJarFile$1.class
com/simontuffs/onejar/OneJarFile$2.class
com/simontuffs/onejar/OneJarFile.class
com/simontuffs/onejar/OneJarURLConnection.class
src/
src/com/
src/com/simontuffs/
src/com/simontuffs/onejar/
src/OneJar.java
src/com/simontuffs/onejar/Boot.java
src/com/simontuffs/onejar/Handler.java
src/com/simontuffs/onejar/IProperties.java
src/com/simontuffs/onejar/JarClassLoader.java
src/com/simontuffs/onejar/OneJarFile.java
src/com/simontuffs/onejar/OneJarURLConnection.java
doc/
doc/one-jar-license.txt
```
仔细和前面一个比较我们就可以很明显地看出区别，那就是包含了第三方库，同时也包含了onejar本身的源码。执行这个`fat jar`看看会不会正常？

``` bash
# Richard at Richards-MacBook-Pro.local in ~/IdeaProjects/FatJar/target [23:41:23]
→ java -jar fat-jar-example.one-jar.jar 
Hello Fat Jar!
Current date: 2017-06-11
```
完全正常！！！说明是可行的！我推荐同学们如果需要使用`Fat Jar`的时候就是用这种方式打包。

## Fat Jar的应用
Fat Jar有哪些应用呢？一般来说，Fat Jar主要是有以下几个方向的应用：

1. 做Java小工具应用程序，我们直接执行这个Jar就可以启动程序。
2. Spring-Boot生成的可执行Jar就是一种Fat Jar。

目前博主能够想到的方向就这两点哈，如果后面想到了继续补充！附上整个项目的Maven配置文件`pom.xml`，大家可以直接拷贝，自己参照着修改一下再build就可以了！

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.qinjiangbo</groupId>
    <artifactId>FatJar</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.5</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>fat-jar-example</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.qinjiangbo.App</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <plugin>
                <groupId>com.jolira</groupId>
                <artifactId>onejar-maven-plugin</artifactId>
                <version>1.4.4</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>one-jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```