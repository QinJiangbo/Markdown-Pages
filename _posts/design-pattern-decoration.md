---
title: 设计模式学习之装饰者模式
date: 2016-12-04 00:18:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/decorator-pattern.png
tags:
	- 装饰者模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 装饰者模式
	- 设计模式
---
## 什么是装饰者模式
**装饰者模式(Decorator Desgin Pattern)**`动态地将责任附加到对象上。若要扩展功能，装饰者提供了比继承更有弹性的替代方案。`

## 给爱用继承的人一个全新的设计眼界
我们会再次讨论继承滥用的问题。我们之前说过，设计模式有一个原则就是`多用组合 ，少用继承。`那么在本设计模式中，我们将讨论如何使用对象组合的方式，在运行时动态地装饰类。这样我们就可以在运行时不修改任何底层代码的情况下动态地将我们自己的（或着别人的）对象赋予新的职责。

## 装饰者模式类图
![装饰者模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/decorator-01.png)
这个类图刚开始一看可能会觉得有点难以理解，别着急，下面给你解释类图中每个模块的作用。

`Component`是所有组件的父类，通常它是一个抽象类。每个组件都可以单独使用，也可以被装饰者包装起来用。这个抽象类有两种子类，分别是具体组件类`ConcreteComponent`和装饰者类`Decorator`。

`ConcreteComponent`类是具体组件类。使我们将要动态地加上新行为的对象，它拓展自`Component`类。

`Decorator`类是装饰者类。每个装饰者都“有一个”（包装一个）组件，也就是说，装饰者有一个实例变量以保存某个`Component`的引用。这个`Decorator`是装饰者共同实现的接口（当然也可以是抽象类）。

`ConcreteDecorator`是具体装饰者，它有一个实例变量，可以记录所装饰的事物（装饰者包着的`Component`）。装饰者可以加上新的方法。新行为是通过在就行为前面或者后面做一些计算来添加的。装饰者可以拓展`Component`的状态。

## 实例分析-星巴兹咖啡
星巴兹（为啥不是星巴克呢？）是以扩张速度闻名的咖啡连锁店。在马路上到处都可以看到它，无处不在啊！因为扩张速度实在是太快了，所以他们呢想更新自己的订单系统。主要的需求如下:

顾客在购买咖啡的时候需要加入不同的调味料，比如说燕奶（Steamed Milk），豆浆（Soy），摩卡（Mocha）或者奶泡。星巴兹会根据用户预定的不同的调料动态地计算用用户所需支付的金额。

这个需求如何实现呢？这个好办啊，我们可以针对每一种可能的调料组合计算不同的价格。如下：
![初次尝试](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/decorator-02.png)
我的第一反应就是“炸了啊”。的确，这是一个“类爆炸”的典型例子。这当然是不可行的！这个设计违背了设计模式中`封装变化`和`多用组合，少用继承`的设计原则。是非常不合理的。那么有没有好的解决办法呢？可以考虑`装饰者模式`。

## 装饰者模式的解决方案
根据装饰者模式，星巴兹咖啡的订单系统的架构图可以设计如下：
![新的订单系统类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/decorator-03.png)
我们可以看到，调料盒和饮品已经被解耦了，这里我们通过组合的方式可以产生用户想要的任意味道的饮品。

`注意`**这里可能会有人问道，这里不是使用了继承么？不是原则上说不能使用继承的么？这问题问的很好！应该这么说，这里用的继承纯粹是为了保证装饰者和被装饰者的类型一致，而我们说的新行为取得则是通过组合的方式获取的。所以，并没有违背我们`多用组合，少用继承`的设计原则。**这里请各位读者朋友多加注意！

## 星巴兹订单系统代码实现
先设计`Beverage`类：

``` java
package com.qinjiangbo.decorator.component;

public abstract class Beverage {

    public String description = "Unknown Beverage";

    public String getDescription() {
        return description;
    }

    public abstract double cost();
}

```

`Beverage`类很简单，让我们看看`CondimentDecorator`类（也就是装饰者）的实现吧：

``` java
package com.qinjiangbo.decorator.decorator;

import com.qinjiangbo.decorator.component.Beverage;

public abstract class CondimentDecorator extends Beverage {
    //重新实现getDescription方法
    public abstract String getDescription();
}

```

哇！更简单不是吗？现在我们重点实现`具体饮料`和`具体调料`的实现类：

### 饮料类的实现
具体饮料`Espresso`类的实现:

``` java
package com.qinjiangbo.decorator.component;

public class Espresso extends Beverage {

    public Espresso() {
        description = "Escpresso";
    }

    @Override
    public double cost() {
        return 2.45;
    }
}

```
具体饮料`Decaf`类的实现:

``` java
package com.qinjiangbo.decorator.component;

public class Decaf extends Beverage {

    public Decaf() {
        description = "Decaf";
    }

    @Override
    public double cost() {
        return 2.30;
    }
}

```
具体饮料`DarkRoast`类的实现:

``` java
package com.qinjiangbo.decorator.component;

public class DarkRoast extends Beverage {

    public DarkRoast() {
        description = "DarkRoast";
    }

    @Override
    public double cost() {
        return 1.25;
    }

}

```
具体饮料`HouseBlend`类的实现:

``` java
package com.qinjiangbo.decorator.component;

public class HouseBlend extends Beverage {

    public HouseBlend() {
        description = "HouseBlend";
    }

    @Override
    public double cost() {
        return 1.50;
    }
}

```

### 调料类的实现
具体调料`Milk`类的实现：

``` java
package com.qinjiangbo.decorator.decorator;

import com.qinjiangbo.decorator.component.Beverage;

public class Milk extends CondimentDecorator {

    Beverage beverage;

    public Milk(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return this.beverage.getDescription() + "+milk";
    }

    @Override
    public double cost() {
        return 0.55 + this.beverage.cost();
    }
}

```

具体调料`Mocha`类的实现：

``` java
package com.qinjiangbo.decorator.decorator;

import com.qinjiangbo.decorator.component.Beverage;

public class Mocha extends CondimentDecorator {

    public Beverage beverage;

    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return this.beverage.getDescription() + "+mocha";
    }

    @Override
    public double cost() {
        return 0.99 + this.beverage.cost();
    }
}

```

具体调料`Soy`类的实现：

``` java
package com.qinjiangbo.decorator.decorator;

import com.qinjiangbo.decorator.component.Beverage;

public class Soy extends CondimentDecorator {

    public Beverage beverage;

    public Soy(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return this.beverage.getDescription() + "+soy";
    }

    @Override
    public double cost() {
        return 0.68 + this.beverage.cost();
    }
}

```

具体调料`Whip`类的实现：

``` java
package com.qinjiangbo.decorator.decorator;

import com.qinjiangbo.decorator.component.Beverage;

public class Whip extends CondimentDecorator {

    public Beverage beverage;

    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public String getDescription() {
        return this.beverage.getDescription() + "+whip";
    }

    @Override
    public double cost() {
        return 0.88 + this.beverage.cost();
    }
}

```

### 最后写一个测试类

``` java
package com.qinjiangbo.decorator.test;

import com.qinjiangbo.decorator.component.DarkRoast;
import com.qinjiangbo.decorator.component.Beverage;
import com.qinjiangbo.decorator.decorator.Milk;
import com.qinjiangbo.decorator.decorator.Soy;
import com.qinjiangbo.decorator.decorator.Whip;

public class TestDecorator {

    public static void main(String[] args) {
        Beverage beverage = new Whip(new Soy(new Milk(new DarkRoast())));
        System.out.println("description: " + beverage.getDescription());
        System.out.println("cost: " + beverage.cost());
    }
}

```

打印结果：

``` bash
description: DarkRoast+milk+soy+whip
cost: 3.36
```

## JDK中的装饰者模式
写过Java代码的同学都知道`java.io`这个常用的包。这里面就包含了装饰者模式的一种实现。不知道大家注意到没有，这里面有一些类和我们上面测试用例里面的类包装的使用方式很接近。下面给大家列出这些类：

![Java.IO类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/decorator-04.png)

大家可以看到这个类图和我们前面星巴兹的订单系统的设计并没有什么不同。这个就是设计模式的妙处！大家细细体会！关于JDK中这个`java.io`包里面装饰类的使用就交给大家自己去动手实践啦~

设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)