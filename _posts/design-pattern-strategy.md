---
title: 设计模式学习之策略模式
date: 2016-06-22 03:54:09
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-pattern.png
tags:
	- 策略模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 策略模式
	- 设计模式
---
## 什么是策略模式
**策略模式（Strategy Design Pattern）**`定义算法族，分别封装起来，让他们之间可以相互替换，此模式让算法的“变化”独立于使用算法的客户`。

## 策略模式类图
![具体实例类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-07.png)

## 策略模式实例分析

先从一个简单的例子开始（盗用Head First图片哈），假设你的公司要你做一个鸭子模拟的游戏SimDuck，这个很简单吧，我们只需要熟悉OO设计原则即可以利用继承，多态来做。于是，我们有了以下的设计图：
![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-01.png)

这幅图我们并不感觉复杂，在Duck类里面定义了quack(), swim(), display()三个方法，然后display()方法被子类重写了，原因其实很简单，因为不同的鸭子他们的外观是不一样的，所以这里我们要重写这个display()方法。

好啦，这样我们的任务就完成了，然后开开心心给老板提交项目，结果老板也觉得还不错。过了两天，老板说，我们的游戏里面鸭子最好能飞就好啦！然后给你改，觉得如何？所以我们就直接在Duck里面加入了fly()方法，如下：
![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-02.png)

这样就好了吗？答案是否定的！因为，并不是所有的鸭子都会飞，所以这样就不行！也许有人还在说，我们可以在子类中重写fly()方法，然后在方法体里面啥也不写！有人会说这样也行？！那么，为什么不行呢？！但是这个不是一个好的设计，因为，如果我们加入一个玩具鸭子呢？模型鸭子呢？他们既不会游泳，也不会叫也不会飞。肿么办？我们是不希望他们具备飞这个属性或者能力的，对吧？但是这种继承的方法会使得我们在子类中存在这个属性或者方法，那是与实际情况不符的！

有人想到了接口，那么用接口会如何呢？<br>
我们可以把我们需要的方法单独写在一个接口里面，供那些子类来调用和实现，比如我们可以有Flyable和Quackable接口，里面分别写上抽象方法fly()， quack()。然后子类继承Duck父类并实现这两个接口。如下图：
![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-03.png)

这样一来我们只需要在需要实现fly()或者quack()的类里面实现相应的接口即可。这样不是很好吗？你觉得呢？方正我当时觉得挺好的，不过当你往下看你就会注意到问题了。假设我们采用这种方式了，那么，我们会遇到什么情况呢？那就是你要多写很多很多行代码，因为每一个鸭子的子类中都会实现这两个方法中的一个，方法重复率太高了！而且我们需要去修改这个方法的时候，我们需要改动的代码数量可是相当庞大的！所以这个方式看似可以，实则不行！

那么我们如何处理这个问题呢？<br>
其实有很多种方法，这里博主介绍自己的方法，那就是将我们项目变化的部分单独提取“封装”起来，让它不对项目的其它部分不受到影响。具体的做法是，我们可以将quack()和fly()两种方法，原则上变化部分还有display()，但是我们的display()方法只有一种类型的行为，也就是只有一个方法，所以这里没有必要把它单独写在一个接口里面，直接写在子类里面实现就好了。
那么，下面我们可以将这个结构画出来了，
![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-04.png)

那么这样一来，我们就可以相同的方法添加许多其他的行为，而不会影响到这个原有的功能。这样程序里面个对象的耦合程度就比较低了，更便于管理和维护。我们需要在Duck类里面引用这两个接口的对象，但是不要求初始化，因为这个初始化的工作是交给子类去做的。那么，你可能会问，不是说“`面向接口编程，不要面向实现`”嘛！额，好吧，的确是这样的，我们也确实这么干了，但是我们要知道，到目前为止，我们还没有接触到其他的设计模式，所以目前就先这么写着，到了后面我们会有解决方法的，比如采用getBehavior()方法来动态地加载某个接口，这样我们就可在运行时动态地实现相关地方法。当然，这个本小节不提到。
那么，我们现在这个设计已经很不错了，至少灵活性很高，而且非常好维护。概念图如下：
![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/strategy-05.png)

我们可以看到，这里面多了两个方法peformQuack()和performFly()，这是为了替换那个接口中得两个方法，我们不希望子类访问到父类的成员变量的方法里面去，这样耦合程度太高，而且也不利于维护。因为我们采用的是继承，所以子类直接能用flyBehavior和quackBehavior两个父类的成员变量。所以，我们就用peformQuack()和performFly()这两个方法来“封装”它。
首先来看父类Duck的源码：

``` Java
public abstract class Duck {
	
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;
	
    public Duck() {
	
    }
	
    public void performFly() {
        flyBehavior.fly();
    }
	
    public void performQuack() {
        quackBehavior.quack();
    }
	
    public abstract void display();
	
    public void swim() {
        System.out.println("All ducks float, even decoys!");
    }
}
```

再看子类MollardDuck的源码：

``` Java
public class MallardDuck extends Duck {
	
    public MallardDuck() {
        quackBehavior = new Quack();
        flyBehavior = new FlyWithWings();
    }
	
    @Override
    public void display() {
        System.out.println("I am a real mallard duck!");
    }
	
}
```

它里面自己实现了display()的方法，打印了一条输出语句。
接着FlyBehavior和QuackBehavior接口的源码：

``` Java
public interface FlyBehavior {
	
    /**
     * 飞行方法
     */
    public void fly();
	
}
	
public interface QuackBehavior {
	
    /**
     * 鸭叫方法
     */
    public void quack();
	
}
```

这两个源码不解释了，很明显。
实现者这两个行为方法的行为子类：
飞行行为的：

``` Java
public class FlyWithWings implements FlyBehavior {
	
    @Override
    public void fly() {
        System.out.println("I am flying!");
    }
}

public class FlyNoWay implements FlyBehavior {
	
    @Override
    public void fly() {
        System.out.println("I can't fly!");
    }
}
```

叫声行为：

``` Java
public class Quack implements QuackBehavior {
	@Override
	public void quack() {
    	System.out.println("Quack!");
	}
}

public class MuteQuack implements QuackBehavior {
	
    @Override
    public void quack() {
        System.out.println("<< Silence! >>");
    }
}
```

关于测试代码很简单，调用一个子类测试一下相关功能就好了。
需求改变啦，需要增加一个模型鸭，然后加上火箭做推动器，然它飞起来。
那么，这个时候呢，我们得先看一下这个需求，需要动态地指定flyBehavior行为对吧？因为它之前不会飞，然后要求它经过改变之后能飞，那就需要我们在运行时给它动态指定行为，这个时候，我们需要在Duck里面增加两个setter方法，如下：

``` Java
public void setQuackBehavior(QuackBehavior quackBehavior) {
    this.quackBehavior = quackBehavior;
}
	
public void setFlyBehavior(FlyBehavior flyBehavior) {
    this.flyBehavior = flyBehavior;
}
```

那么我们就可以动态指定方法了。然后需要增加一个火箭助推行为：

``` Java
public class FlyRocketPowered implements FlyBehavior {
	
    @Override
    public void fly() {
        System.out.println("I am flying with a rocket!");
    }
}
```

这样，我们就有了动态指定的对象了。测试代码如下：

``` Java
public class Test2 {
	
    public static void main(String[] args) {
	
        Duck modelDuck = new ModelDuck();
        modelDuck.performFly();
        modelDuck.setFlyBehavior(new FlyRocketPowered());
        modelDuck.performFly();
    }
}
```

运行结果是：
``` bash
I can't fly!
I am flying with a rocket!
```

是不是觉得这种方式挺方便的呢？支持拓展和维护，系统是非常具有弹性的。不管以后增加什么行为都可用相同的方式添加进去。当然啦，实际生产生活中得情况肯定比这个复杂很多，所以我们要根据实际情况来定。

### 关于策略模式我们有几点需要提醒大家：
OO的基础：`抽象`，`封装`，`多态`，`继承`
OO的原则：

1. 封装“变化”；
2. 多用组合，少用继承；
3. 针对接口编程，不要针对实现编程；
     
第（3）点简单举个例子读者自己好好体会，感受一下区别：

### 针对实现编程：

``` java
Dog dog = new Dog();
dog.bark();
```

### 针对接口编程：

``` java
Animal animal = new Dog(); //上转型接口回调
animal.makeSound();
```

**适用范围**：适用于那些外在表现行为不同的场合，比如出行方式，通信方式等等，这些都是有不同的表现形式的，我们此时就可以采取策略模式。

设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)