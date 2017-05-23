---
title: 设计模式学习之观察者模式
date: 2016-11-05 01:25:44
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/observer-pattern.png
tags:
	- 观察者模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 观察者模式
	- 设计模式
---
## 什么是观察者模式
**观察者模式(Observer Design Pattern)**`定义了对象之间的一对多依赖，这样一来，当一个对象改变状态的时候，它的所有依赖者都会收到通知并自动更新。`

## 出版者+订阅者=观察者模式
如果你了解报纸的订阅方式，我想你基本上也就知道了观察者模式是怎样的一回事了。在观察者模式中我们一般使用`主题`来表示`出版者`，用`观察者`来表示`订阅者`。

## 观察者模式类图

![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/observer-01.png)

可以看到整个类图非常简单，定义了两个接口`Subject`和`Observer`，然后`Subject`中包含了`Observer`的引用，以便于数据变化时能及时调用`Observer`的更新方法。`Subject`和`Observer`分别有自己的具体实现类，`ConcreteSubject`和`ConcreteObserver`。这个很重要，我们说过一个原则，`针对接口编程，不针对实现编程`。另外，大家也看到，具体的观察者`ConcreteObserver`中包含了一个`ConcreteSubject`的引用，这个是为了在`Subject`注册自己用的。

## 实例分析-气象站
现在有一个气象站找到你做一个需求，要求做一个天气状况和天气预报推送的软件。这个系统分为三个部分。**气象站**（获取实际气象数据的物理装置）、**天气数据对象**（追踪天气天气数据，并更新布告板）以及**布告板**（显示天气给用户看）。

系统的结构简单描述下

![](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/observer-02.png)

如果我们接受这个项目，那我们需要做的事情就是建立一个应用，利用这个WeatherData对数据采样并及时更新三个布告板：目前状况、气象统计和天气预报。

好了，怎么做呢？我们挨个分析。

### WeatherData气象数据类（主题）
我们需要弄清楚这个WeatherData的具体功能是什么？目前已知的就是

- WeatherData类具有`getter`方法，利用这个方法它可以获取各个观测指标，`温度、湿度和气压`。
- 其次当观测数据变化的时候，它需要通知布告板更新，这里我们定义一个方法来说明它`measurementsChanged()`，一旦数据发生变化，这个方法就会被调用。
- 我们需要实现三个布告板来显示天气的数据，`目前状况`，`气象统计`和`天气预报`。

### 布告板类（订阅者）
布告板类的功能非常简单，被通知更新，以及显示天气的数据。

- 被通知更新，所以布告板里面需要提供一个更新方法，以便于及时更新布告板当前的数据。我们使用`update`方法来表示。
- 显示各个指标，这里我们使用`display`来表示。

### 代码实现
首先是WeatherData，这里我们不讨论这个类的getter方法是如何获取数据的，因为这个涉及到物理方面的知识，我们就认为气象站给我们实时提供了这些数据，我们直接获取就行了。

先定义一个Subject接口

``` java
package com.qinjiangbo.observer.subject;

import com.qinjiangbo.observer.observer.Observer;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 4:55 PM
 * Author: Richard
 */
public interface Subject {

    //注册观察者
    public void registerObserver(Observer o);

    //移除观察者
    public void removeObserver(Observer o);

    //计算观察者数量
    public int countObservers();

    //通知观察者
    public void notifyObservers();
}

```
接着实现Subject接口

``` java
package com.qinjiangbo.observer.subject;

import com.qinjiangbo.observer.observer.Observer;

import java.util.ArrayList;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 5:00 PM
 * Author: Richard
 */

//push
public class WeatherData implements Subject {


    private ArrayList<Observer> list;
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
        list = new ArrayList<Observer>();
    }

    @Override
    public void registerObserver(Observer o) {
        list.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        int index = list.indexOf(o);
        if(index != -1) {
            list.remove(index);
        }
    }

    @Override
    public int countObservers() {
        return list.size();
    }

    @Override
    public void notifyObservers() {
        for (Observer observer: list) {
            observer.update(temperature, humidity, pressure);
        }
    }

    public void measurementsChanged() {
        notifyObservers();
    }

    /**
     * 设置各参数
     * @param temperature
     * @param humidity
     * @param pressure
     */
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}

```

那么我们的`主题`就实现了。在主题里面我们统计了`订阅者`的数量，并且允许`注册订阅者`，当数据有更新时及时通知`订阅者`更新。

现在定义一个`订阅者`Observer

``` java
package com.qinjiangbo.observer.observer;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 4:56 PM
 * Author: Richard
 */
public interface Observer {

    //更新天气信息
    public void update(float temp, float humidity, float pressure);
}

```

根据需求分析我们需要实现两个`订阅者`，分别显示不同的资讯。但是显示的功能是公共的，这个可以单独提出来作为一个接口。

``` java
package com.qinjiangbo.observer.observer;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 4:59 PM
 * Author: Richard
 */
public interface DisplayElement {

    //显示数据
    public void display();
}

```
**当前天气状态和气象数据**

``` java
package com.qinjiangbo.observer.observer;

import com.qinjiangbo.observer.subject.Subject;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 5:15 PM
 * Author: Richard
 */
public class CurrentConditionDisplay implements Observer, DisplayElement {

    private float temperature;
    private float humidity;
    private float pressure;

    public CurrentConditionDisplay(Subject weatherData) {
        weatherData.registerObserver(this);
    }

    @Override
    public void display() {
        System.out.println("current conditions: " + temperature + " F degrees and "+ humidity + "% humidity and pressure " + pressure);
    }

    @Override
    public void update(float temp, float humidity, float pressure) {
        this.temperature = temp;
        this.humidity = humidity;
        this.pressure = pressure;
        display();
    }
}

```

**天气预报**

``` java
package com.qinjiangbo.observer.observer;

import com.qinjiangbo.observer.subject.WeatherData2;

import java.util.*;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 5:52 PM
 * Author: Richard
 */
public class ForecastDisplay implements java.util.Observer, DisplayElement {

    private float temperature;
    private float humidity;
    private float pressure;
    @SuppressWarnings("unused")
	private Observable observable;

    public ForecastDisplay(Observable observable) {
        this.observable = observable;
        observable.addObserver(this);
    }

    @Override
    public void display() {
        System.out.println("current conditions: " + temperature + " F degrees and "+ humidity + "% humidity and pressure " + pressure);
    }

    @Override
    public void update(Observable o, Object arg) {
        if (o instanceof WeatherData2) {
            WeatherData2 weatherData2 = (WeatherData2) o;
            this.temperature = weatherData2.getTemperature();
            this.humidity = weatherData2.getHumidity();
            this.pressure = weatherData2.getPressure();
            display();
        }
    }
}

```

最重要的是需要来一个测试，来认识一下模式是如何工作的？

``` java
package com.qinjiangbo.observer.test;

import com.qinjiangbo.observer.observer.CurrentConditionDisplay;
import com.qinjiangbo.observer.subject.WeatherData;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/16/15 5:21 PM
 * Author: Richard
 */
public class WeatherStation {

    public static void main(String[] args) {

        WeatherData weatherData = new WeatherData();
        @SuppressWarnings("unused")
		CurrentConditionDisplay currentConditionDisplay = new CurrentConditionDisplay(weatherData);
        weatherData.setMeasurements(80, 65, 30.4f);
    }
}

```

结果

``` bash
current conditions: 80.0 F degrees and 65.0% humidity and pressure 30.4
```

## Java中的观察者模式
我们需要了解的是，Java也为我们提供了观察者模式非常好的实现，大家可以在`java.util.Observerable`和`java.util.Obserever`中查看。不知道发现没有，Java中的`主题`实现`java.util.Observerable`是一个具体的实现类！！！这不符合我们的设计要求啊！没办法，它就这么设计的，在JDK1.0版本就已经确定了，不过这会导致很多问题，比如我们继承这个类，但同时我需求添加许多其他的功能，这个直接限制了我们的复用。因为毕竟Java不支持多继承。

## 观察者模式总结
观察者模式有很多广泛的应用，我们最熟悉就是Java Swing的监听事件的实现，用的就是观察者模式。使用过Java Swing的都知道每个控件我们都需要注册一些监听器，以便于相应对应的用户操作。

我们也说过，JDK中的观察者模式的实现是有很多缺陷的，但如果符合你的实际需求，可以使用，不过博主推荐你还是自己实现一个，因为这样灵活性和复用性更高。毕竟提升代码的复用性和灵活性不一直是我们研究设计模式追求的最终目标吗？

设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)
