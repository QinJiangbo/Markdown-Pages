---
title: 设计模式学习之适配器模式
date: 2017-04-01 11:01:46
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/adaptor-pattern.png
tags:
	- 适配器模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 适配器模式
	- 设计模式
---
## 什么是适配器模式
**适配器模式(Adapter Design Pattern)**`将一个类的接口，转换成客户期望的另一个接口，适配器让原本接口不兼容的类可以合作无间。`

## 将圆假装成正方形
我们知道适配器模式的作用就是将一个类的接口进行转换，以适应客户的要求。那我们现在有一个圆，**如何将一个圆假装成正方形呢？**咋一看没什么头绪，但是学习完本文以后你就知道如何操作了。

## 适配器模式类图
![适配器模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/adapter-01.png)

先说说`Client`类，这个类比较简单，就是一个客户类，这个客户类只能看到目标接口，他并不知道这个接口背后到底是什么东西。但是只要客户类认为是他想要的东西就可以了。

再说说`Target`接口，这个接口是所有适配器实现的目标接口。它定义了一个适配器的一些基本功能。

关于`Adapter`类，它主要是实现了`Target`类，里面的`request()`方法则是它包含的具体的操作了。这个方法里面具体如何实现取决于它是否包含被适配类`Adaptee`。

`Adaptee`很明显是一个被适配类，这类我们采用组合的方式将适配类和被适配类组合起来完成相应的适配工作。`Adaptee`里面的`specificRequest()`方法其实是在`Adapter`里面的`request()`中完成调用的。

## 实例分析-伪装成鸭子
之前看[廖雪峰老师](http://www.liaoxuefeng.com/)的官方博客的时候就看到了一句话`如果一件事看起来像某个东西，那么系统就可以认为它就是这个东西。`当时没看太明白，但是现在相信大家伙都能明白说的是什么了。这句话其实是在他的`Python`教程里面说的，说的是**如果一个对象具有`read`和`write`等方法，那么我们完全可以把它当做是文件来看待。**那我们这里的这个实例其实就可以说成**如果一个小动物走起路来并且叫起来像鸭子，那我们认为它就是一只鸭子。**

### 目标接口-Duck
我们这里使用Duck接口作为我们的目标接口。这个接口中我们先定义两个行为：

- `quack()`这个方法就是呱呱叫啦！
- `fly()`鸭子会飞吗？应该会吧！哈哈！

### 适配类-TurkeyAdapter
这个没什么要说的，这个类实现了前面的目标接口，所以上面提到的两个方法它都必须实现，但是至于具体怎么实现，还不太清楚。我们也知道适配类和被适配类是通过组合的方式来进行功能的包装的，那么是如何组装的呢？看这个方法：`Duck duck = new TurkeyAdapter(new Adaptee);`，通过在构造函数里面传递这个被适配类的实例来实现功能的包装。

### 被适配类-Turkey
火鸡🦃！火鸡和鸭子有一点相似的地方就是它们都是禽类。那我们来看一看火鸡有哪些行为呢？

- `gobble()`这个方法就是咯咯叫啦！
- `fly()`火鸡也会飞，但是飞不远。

### 具体实现类
`Duck`接口的具体实现类我们选一个，那就`MallardDuck`绿头鸭呗！`Turkey`接口的具体实现类我们也得有一个，选啥呢，那就`WildTurkey`野火鸡呗！

## 实例代码实现

Duck

``` java
public interface Duck {
    public void quack();
    public void fly();
}
```

MallardDuck

``` java
public class MallardDuck implements Duck {

    @Override
    public void quack() {
        System.out.println("Quack");
    }

    @Override
    public void fly() {
        System.out.println("I'm flying");
    }
}
```

Turkey

``` java
public interface Turkey {
    public void gobble();
    public void fly();
}
```

WildTurkey

``` java
public class WildTurkey implements Turkey {

    @Override
    public void gobble() {
        System.out.println("Goggle goggle");
    }

    @Override
    public void fly() {
        System.out.println("I'm flying a short distance");
    }
}
```

TurkeyAdapter

``` java
public class TurkeyAdapter implements Duck {

    Turkey turkey;

    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }

    @Override
    public void quack() {
        turkey.gobble();
    }

    @Override
    public void fly() {
        for (int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}
```

好，开工测试一下，写一个客户端类：

``` java
public class Client {

    public static void main(String[] args) {

        Duck mallardDuck = new MallardDuck();
        Turkey wildTurkey = new WildTurkey();
        Duck turkeyAdapter = new TurkeyAdapter(wildTurkey);

        System.out.println("The turkey says...");
        wildTurkey.gobble();
        wildTurkey.fly();

        System.out.println();

        System.out.println("The duck says...");
        mallardDuck.quack();
        mallardDuck.fly();

        System.out.println();

        System.out.println("The turkey adapter says...");
        turkeyAdapter.quack();
        turkeyAdapter.fly();
    }
}
```

#### 测试结果
``` bash
The turkey says...
Goggle goggle
I'm flying a short distance

The duck says...
Quack
I'm flying

The turkey adapter says...
Goggle goggle
I'm flying a short distance
I'm flying a short distance
I'm flying a short distance
I'm flying a short distance
I'm flying a short distance
```

## 总结
关于适配器模式的例子在我们的生活中还有很多，比如说我们的充电器适配器。苹果的充电器适配器不仅能连接`iPhone 7`还能连`iPad Pro`等等。说明他们的接口首先是一致，另外这个适配器也做了相应的适配处理。适配器模式让我们将两个不同的对象通过巧妙地组合密切地联系在了一起。**组合的方式更灵活，更利于系统的拓展。不建议大家使用继承去拓展功能！**