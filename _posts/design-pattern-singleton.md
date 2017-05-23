---
title: 设计模式学习之单例模式
date: 2016-12-05 22:04:52
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/singleton-pattern.png
tags:
	- 单例模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 单例模式
	- 设计模式
---
## 什么是单例模式
**单例模式（Singleton Desgin Pattern）**`确保一个类只有一个实例，并提供一个全局的访问点。`

## 为啥使用单例模式
使用单例模式有什么好处呢？一般来说，我们在程序编写中，有些对象我们只需要一个，比如：线程池（threadpool）、缓存（cache）、对话框、处理偏好设置以及注册表（registry）的对象、日志对象等等。事实上，有些对象其实只能有一个，因为如果有多个的话程序可能还会出问题，或者是导致资源使用过量的问题。

## 单例模式类图
![单例模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/singleton-01.png)

这个应该是史上最简单的类图啦~不过还是需要解释一下：

`uniqueInstance`类变量持有唯一的单例实例。单例类可以是非常普通的类，拥有非常普通的方法。

`getInstance()`方法是静态的，这意味着它是一个类方法，所以可以在代码的任何地方使用`Singleton.getInstance()`访问它。这和访问全局变量一样简单，只是多了一个优点：**单例可以延迟实例化**。

## 单例模式具体实现
这一篇我们就不举（扯）实（犊）例（子）啦，直接上代码，代码很简单，非常容易理解。

``` java
package com.qinjiangbo.singleton;

public class Singleton {

    public static Singleton instance;

    /**
     * 防止直接实例化
     */
    private Singleton() {

    }

    /**
     * 双重检查锁
     * @return
     */
    public static Singleton getSingletonInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```

## 单例模式 VS 全局变量
我们前面说了单例模式可以提供一个全局的访问点，保证该单例类的对象在全局只实例化一次。全局变量（也就是类变量）也可以保证全局只有一个访问点，为什么我们不提倡使用它呢？

这里需要说明的是，的确，全局变量能为我们提供全局的访问点。但是，我们需要知道，如果将一个对象赋值给一个全局变量，那么你必须在程序一开始就创建好对象，对吧？万一这个对象非常的耗资源，而程序在此时并不需要使用这个对象，不就很浪费吗？我们之所以使用单例模式，还有一个重要的原因就是这个，**单例模式能够保证对象的延迟实例化**。这个是非常重要的一个理念，只有当程序需要的时候才进行实例化，有利于程序发挥最大的性能。

`注意` 单例模式中，**单例类的构造函数必须声明为private**，切记！不然该类就能在全局各个地方进行实例化了。

设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)