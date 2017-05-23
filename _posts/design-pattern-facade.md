---
title: 设计模式学习之外观模式
date: 2017-04-01 13:47:24
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/facade-pattern.png
tags:
	- 外观模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 外观模式
	- 设计模式
---
## 什么是外观模式
**外观模式(Facade Design Pattern)**`提供了一个统一的接口，用来访问子系统中的一群接口。外观模式定义了一个高层接口，让子系统更容易使用。`

## 外观模式类图
![外观模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/facade-01.png)

可以看到，这个设计模式非常的简单，简单到我们可以一眼就知道它的功能。不过我们还是来分析一下这个图中各个类的具体含义。

先说说`Client`类，这个是客户类，因为有了外观，客户的工作更加容易了，它不需要挨个挨个去调用系统中的各个元件的功能，只需要调用外观中封装好的功能即可。

再说说我们的核心类`Facade`，这个是外观类，它负责将子系统中的各个功能通过一个统一的方法封装起来，提供给客户类调用，简化客户类的工作。子系统当然是非常复杂的，我们这里就不讨论子系统的具体实现了。

## 实例分析-家庭影院
假设你想搭建一个家庭影院，希望在家里也能感受到电影院般的氛围。那么你需要准备好多东西：

- 爆米花🍿
- 灯光
- 投影仪
- DVD
- 功放

假设就这五个吧。那你需要如何才能开始准备好呢，我想应该是这样的：

1. 打开爆米花机
2. 开始爆米花
3. 调暗灯光有氛围
4. 打开投影仪
5. 打开功放
6. 打开DVD播放器
7. 开始播放DVD

累不累啊！这么多手续，其实我们只是想放个电影而已，而这些都是一些流程化手续，能不能不要我们手动去做啊，直接**一键完成**不就OK了吗？那么我们来看看如何一键完成。

## 代码实现
这里说明一下，这里只列出`Facade`这个主要的类，子系统的类太多，它们的具体实现就不一一列出来了。

OneKeyButton

``` java
public class OneKeyButton {
	private Popper popper;
	private Light light;
	private Projector projector; // 投影仪
	private  DvdPlayer dvdPlayer;
	private Tunner tunner;
	
	public OneKeyButton(Popper popper, Light light, Projector projector,
			DvdPlayer dvdPlayer, Tunner tunner) {
		this.popper = popper;
		this.light = light;
		this.projector = projector;
		this.dvdPlayer = dvdPlayer;
		this.tunner = tunner;			
	}
	
	public void watchMovie(String movieName) {
		popper.on();
		popper.pop();
		light.dim(10);
		projector.scrollDown();
		tunner.on();
		dvdPlayer.play(movieName);
		tunner.volumeUp(40);
	}
	
	public void endMovie() {
		popper.off();
		light.light(10);
		projector.scrollUp();
		tunner.off();
		dvdPlayer.off();
	}
}
```
大家看看是不是简洁多了！我们`Client`客户类调用的时候只需要调用`watchMovie()`和`endMovie()`这两个方法即可，无需关心里面具体是如何实现的。是不是很Nice？这里需要给大家介绍一种设计模式中的原则之一`最少知识原则`，也叫`迪米特法则`。

## 最少知识原则（迪米特法则）
什么是最少知识原则？`最少知识原则告诉我们要减少对象之间的交互，只留下几个“密友”，也就是说只和他自己的“密友”交谈。`

### 究竟我们才能如何不要赢得太多的朋友和影响太多的对象？
这个原则提供了一些方针：

- 该对象本身
- 被当做方法的参数而传递进来的对象
- 此方法所创建或者实例化的任何对象
- 对象的任何组件（HAS-A关系）

到底是啥嘛！我们来举个栗子🌰！

#### 不采用这个原则：

``` java
public float getTemp() {
	Thermometer thermometer = station.getThermometer();
	return thermometer.getTemperature();
}
```

#### 采用这个原则：

``` java
public float getTemp() {
	return station.getTemperature();
}
```

大家明白区别了吗？前面一个通过`station`获取了`Thermometer`对象，然后通过`Thermometer`对象来间接获取温度信息。但问题是，我们需要知道`Thermometer`对象吗？？？不需要！！！所以，我们直接在`station`里面封装这个方法就行了。**这样更简洁，更利于代码的维护和拓展。**

## 总结
虽然外观模式比较简单，但是从这个模式里体现出来的`最少知识原则`却是我们应该注意的，这个原则提醒我们要时刻保证各个对象之间的耦合性，不要让一个对象过多地知道与它无关的对象的信息。
