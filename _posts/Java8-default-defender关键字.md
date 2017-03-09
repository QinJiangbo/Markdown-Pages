---
title: 'Java8-default,defender关键字'
date: 2016-06-26 23:08:25
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-thumbnail.png
tags:
	- JDK8
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
---
![JDK 8](https://obrxbqjbi.qnssl.com/blog/image/java8-thumbnail.png)

今天研究Java8源码的时候发现了一个神奇的关键字，default，我一直以为default只是在switch里面起作用，其余的就没什么了。然而，知道我看到它作为方法限定符之后我的三观刷新了。。。居然还可以作为权限访问符！

写了几个例子研究一下：
源码1：

``` java
public interface DefaultAPI {

    public void add(int a, int b);

    default void minus(int a, int b) {
        System.out.println("a-b="+(a-b));
    }
}
```

```java
public interface DefaultAPI2 {

    default void minus(int a, int b) {
        System.out.println(a+"-"+b+"="+(a-b));
    }

    public void add(int a, int b);
}
```

从源码可以看出，两个接口的方法都相同，只是打印的有点区别。那么这会有什么影响呢？

**子类继承这两个接口的时候，需要重写这个方法，而这两个方法已经被实现啦！没错，interface接口中可以写具体实现方法啦！**

这个作用是为了**lambda表达式与之前的jdk接口紧密联系起来，向下兼容**，更方便开发者利用计算机的资源。
