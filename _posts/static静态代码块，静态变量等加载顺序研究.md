---
title: static静态代码块，静态变量等加载顺序研究
date: 2016-04-12 21:43:13
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-2-thumbnail.png
tags:
	- JDK8
	- static
	- 加载顺序
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
	- static
	- 加载顺序
---
一直很纠结static代码块和static静态变量的加载顺序，网上的教程良莠不齐，决定自己亲自试一下，毕竟实践才是检验真理的唯一标准，实践过才有发言权！好啦，先看代码：

父类代码:

``` java
public class StaticBlock {

    {
        System.out.println("Just one parent normal line!");
    }

    private final static int e = 8;
    {
        System.out.println("I am final static field e!");
    }

    private static int d = 7;
    {
        System.out.println("I am static field d!");
    }

    static {
        System.out.println("I am static block!");
    }

    private static int a = 7;
    {
        System.out.println("I am static field a!");
    }

    private final static int b = 8;
    {
        System.out.println("I am final static field b!");
    }

    public StaticBlock(){
        System.out.println("I am constructor!");
    }

    public int c = 9;
}
```

子类代码:

``` java
public class SubStaticBlock extends StaticBlock {
    {
        System.out.println("Just one sub normal line!");
    }

    private final static int e = 8;
    {
        System.out.println("I am sub final static field e!");
    }

    private static int d = 7;
    {
        System.out.println("I am sub static field d!");
    }

    static {
        System.out.println("I am sub static block!");
    }

    private static int a = 7;
    {
        System.out.println("I am sub static field a!");
    }

    private final static int b = 8;
    {
        System.out.println("I am sub final static field b!");
    }

    public SubStaticBlock(){
        System.out.println("I am sub constructor!");
    }

    public int c = 9;
}
```

子类与父类在打印语句里面最大的区别就是多了一个sub对吧。

好，我们**先单纯地看一下父类的代码执行情况**：

``` bash
I am static block!
Just one parent normal line!
I am final static field e!
I am static field d!
I am static field a!
I am final static field b!
I am constructor!
c is 9
```

**优先加载的是static代码块，其次是写在最前面的普通代码块，然后才是第二的final static 静态变量e，接着就是static变量d和a，然后又是final static变量b，最后是constructor对吧，就是这个顺序的。**

那么子类加载的情况如何呢？

``` bash
I am static block!
I am sub static block!
Just one parent normal line!
I am final static field e!
I am static field d!
I am static field a!
I am final static field b!
I am constructor!
Just one sub normal line!
I am sub final static field e!
I am sub static field d!
I am sub static field a!
I am sub final static field b!
I am sub constructor!
c is 9
```

**可以看到，static代码在子类和父类都优先执行了，其次是父类的代码按照顺序先执行，再次是子类的代码按照顺序执行。**