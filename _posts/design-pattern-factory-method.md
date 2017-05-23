---
title: 设计模式学习之工厂方法模式
date: 2016-12-04 19:56:38
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/factory-method.png
tags:
	- 工厂方法模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 工厂方法模式
	- 设计模式
---
## 什么是工厂方法模式
**工厂方法模式(Factory Method Design Pattern)**`定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到了子类。`

## 是时候从`new`中解放出来啦
当我们看到`new`，就会立马想到`具体`。是的，的确是这样的，我们在使用`new`创建一个对象的时候，我们实际上是针对实现编程，而不是针对接口。设计模式中有一个原则就是`针对接口编程而不是针对实现编程`。针对接口编程，可以隔离掉以后系统可能发生的一大堆**改变**。为什么呢？因为代码是针对接口编写的，通过多态，它可以与任何新类实现该接口。但是，当代码使用大量的具体类时，等于自找麻烦，因为一旦加入新的具体类，我们就必须改代码。

## 工厂方法模式类图
![工厂方法模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/factory-method-01.png)

`Product`是所有的产品必须实现的一个共同的接口，这样一来，使用这些产品的类就可以引用这个接口，而不是具体类。

`Creator`是一个抽象创建者类，它实现了所有操作产品的方法，但不实现工厂方法。它的子类必须实现这个`factoryMethod()`方法。

`ConcreteProduct`是具体产品类，实现了`Product`接口。

`ConcreteCreator`类是具体创建者类，它实现了`factoryMethod()`方法，知道如何创建一个或者多个具体产品，只有`ConcreteCreator`类知道如何创建这些产品。

## 实例分析-披萨店(Pizza Store)

假设你开了一家披萨店，生意不错，每天来你的店子吃披萨的人很多。你不小心告诉我你是这么做披萨的：

``` java
Pizza orderPizz() {
	Pizza pizza = new Pizza();
	// 制作过程，绝密！	
	pizza.prepare();
	pizza.bake();
	pizza.cut();
	pizza.box();
	return pizza;
}
```

为了满足市场的需求，你决定增加一些口味的披萨，但是你不想自己亲手去制作每一个披萨啦，你希望有一个代工厂能帮你做，你付给他一定的酬劳，他负责帮你创建这些披萨。

``` java
package com.qinjiangbo.factory.simpleFactory;

public class SimpleFactory {

    private Pizza pizza;

    public SimpleFactory() {
        //no args
    }

    /**
     * 生产Pizza对象
     * @param type
     * @return
     */
    public Pizza createPizza(String type) {

        if (type.equals("Pepperoni")) {
            pizza = new PepperoniPizza();
        }
        else if (type.equals("Clam")) {
            pizza = new ClamPizza();
        }
        else if (type.equals("Veggie")) {
            pizza = new VeggiePizza();
        }
        else if (type.equals("Cheese")) {
            pizza = new CheesePizza();
        }
        else {
            System.out.println("Invalid Type!");
        }
        return pizza;
    }
}

```

似乎问题好像变得容易多了，至少下一次你想去掉或者增加一个新品种的披萨时，只需要跟代工厂说一下（**只需要修改这一处的代码**）。但是如果我想要开另一家店呢？

### 卑鄙的我
我也看到了商机啊，我说，给你保守这个秘密可以，但是前提是我自己加盟你的披萨店，成立一个连锁店，如何？你说：嗯，好啊！太好了！（缺心眼？？哈哈）于是我就顺（臭）理（不）成（要）章（脸）地成立了自己的披萨店。我们使用同样一个代工厂帮我们生产披萨，但是我有自己的想法，我说，我需要研发自己的一种披萨风味，和你的不一样，你的是纽约风味，我的要做成芝加哥风味。这个时候，代工厂这边就需要做很大的改变了，他需要**为两家店同时制作不同口味的披萨**。不过还好我负责和代工厂老板沟通具体制作事宜。

## 披萨连锁店系统的结构图
![系统的结构图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/factory-method-02.png)

这张图很好理解，但是还是需要解释一下，右边是创建者类，它是一个抽象类，定义了一个抽闲的方法，下面的`NYPizzaStore`和`ChicagoPizzaStore`是这个创建者类的两个实现类，分别负责创建不同风味的披萨。这个也就代表了我们两家不同的连锁店。

左边的这个很重要，你还是坚持你的纽约风味的披萨，但是我坚持采用全新的芝加哥风味，所以我们两家的披萨产品虽然都源自于`Pizza`这个产品类别，但是已经完全不一样了。

### 披萨生产流程说明
`ChicagoPizzaStore`只负责生产芝加哥风味的披萨，`NYPizzaStore`只负责生产纽约风味的披萨。职责相当明确，不能混合生产，必须独立分开生产。

## 开工生产
### 创建产品（Product）
先创建所有产品的父类`Pizza`：

``` java
package com.qinjiangbo.factory.factoryMethod;

import java.util.ArrayList;

public abstract class Pizza {

    String name;
    String dough;
    String sauce;
    @SuppressWarnings("rawtypes")
	ArrayList toppings = new ArrayList();

    void prepare() {
        System.out.println("Preparing " + name);
        System.out.println("Tossing dough...");
        System.out.println("Adding sauce...");
        System.out.println("Adding toppings: ");
        for (int i = 0; i < toppings.size(); i++) {
            System.out.println("   " + toppings.get(i));
        }
    }

    void bake() {
        System.out.println("Bake for 25 minutes");
    }

    void cut() {
        System.out.println("cut the pizza into triangle slices");
    }

    void box() {
        System.out.println("Place pizza in a box");
    }

    public String getName() {
        return this.name;
    }
}

```

然后创建各类纽约风味的披萨：

`NYCheesePizza`

``` java
package com.qinjiangbo.factory.factoryMethod;

public class NYCheesePizza extends Pizza {

    @SuppressWarnings("unchecked")
	public NYCheesePizza() {
        name = "NY style Sauce and Cheese Pizza";
        dough = "Thin Crust dough";
        sauce = "Marinara Sauce";
        toppings.add("Grated Raggiano Cheese");
    }
}

```
`NYClamPizza`

``` java
package com.qinjiangbo.factory.factoryMethod;

public class NYClamPizza extends Pizza {

    public NYClamPizza() {
        System.out.println("New York Clam Pizza!");
    }
}

```
`NYPepperoniPizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class NYPepperoniPizza extends Pizza {

    public NYPepperoniPizza() {
        System.out.println("New York Pepperoni Pizza!");
    }
}

```
`NYVeggiePizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class NYVeggiePizza extends Pizza {

    public NYVeggiePizza() {
        System.out.println("New York Veggie Pizza!");
    }
}

```

好，接下来创建芝加哥风味的披萨：

`ChicagoCheesePizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class ChicagoCheesePizza extends Pizza {

    @SuppressWarnings("unchecked")
	public ChicagoCheesePizza() {
        name = "Chicago style Sauce and Cheese Pizza";
        dough = "Thin Crust dough";
        sauce = "Marinara Sauce";
        toppings.add("Grated Raggiano Cheese");
    }

    void cut() {
        System.out.println("Cutting the pizza into square slices");
    }
}

```

`ChicagoClamPizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class ChicagoClamPizza extends Pizza {

    public ChicagoClamPizza() {
        System.out.println("Chicago Clam Pizza!");
    }
}

```

`ChicagoPepperoniPizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class ChicagoPepperoniPizza extends Pizza {

    public ChicagoPepperoniPizza() {
        System.out.println("Chicago Pepperoni Pizza!");
    }
}

```
`ChicagoVeggiePizza `

``` java
package com.qinjiangbo.factory.factoryMethod;

public class ChicagoVeggiePizza extends Pizza {

    public ChicagoVeggiePizza() {
        System.out.println("Chicago Veggie Pizza!");
    }
}

```

### 创建生产者（Creator）
先创建所有生产者的父类

``` java
package com.qinjiangbo.factory.factoryMethod;

public abstract class PizzaStore {

    public Pizza orderPizza(String type) {

        Pizza pizza = createPizza(type);

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }

    protected abstract Pizza createPizza(String type);
}

```

再创建你的纽约风味披萨店：

``` java
package com.qinjiangbo.factory.factoryMethod;

public class NYPizzaStore extends PizzaStore {

    @Override
    protected Pizza createPizza(String type) {
        if (type.equals("Pepperoni")) {
            return new NYPepperoniPizza();
        }
        else if (type.equals("Clam")) {
            return new NYClamPizza();
        }
        else if (type.equals("Veggie")) {
            return new NYVeggiePizza();
        }
        else if (type.equals("Cheese")) {
            return new NYCheesePizza();
        }
        else {
            System.out.println("Invalid Type!");
            return null;
        }
    }
}

```
接着创建我的芝加哥风味披萨店：

``` java
package com.qinjiangbo.factory.factoryMethod;

public class ChicagoPizzaStore extends PizzaStore {

    @Override
    protected Pizza createPizza(String type) {
        if (type.equals("Pepperoni")) {
            return new ChicagoPepperoniPizza();
        }
        else if (type.equals("Clam")) {
            return new ChicagoClamPizza();
        }
        else if (type.equals("Veggie")) {
            return new ChicagoVeggiePizza();
        }
        else if (type.equals("Cheese")) {
            return new ChicagoCheesePizza();
        }
        else {
            System.out.println("Invalid Type!");
            return null;
        }
    }
}

```

### 现在开始接受预定

``` java
package com.qinjiangbo.factory.test;

import com.qinjiangbo.factory.factoryMethod.ChicagoPizzaStore;
import com.qinjiangbo.factory.factoryMethod.PizzaStore;
import com.qinjiangbo.factory.factoryMethod.NYPizzaStore;

public class FactoryMethodTest {

    public static void main(String[] args) {

        PizzaStore nyStore = new NYPizzaStore();
        PizzaStore chicagoStore = new ChicagoPizzaStore();

        nyStore.orderPizza("Cheese");
        chicagoStore.orderPizza("Cheese");
    }
}

```

运行结果：

``` bash
Preparing NY style Sauce and Cheese Pizza
Tossing dough...
Adding sauce...
Adding toppings: 
   Grated Raggiano Cheese
Bake for 25 minutes
cut the pizza into triangle slices
Place pizza in a box
Preparing Chicago style Sauce and Cheese Pizza
Tossing dough...
Adding sauce...
Adding toppings: 
   Grated Raggiano Cheese
Bake for 25 minutes
Cutting the pizza into square slices
Place pizza in a box
```

## 总结
工厂方法模式是一个非常好的实践，它实现了面向接口编程，将我们从`new`中解放了出来。但是千万别以为我们没有使用`new`啊，这里需要明确指出，我们只是将具体的实例化类的方法封装起来了，主要是便于统一管理，而且必须使用`new`，这里只不过相当于是把`new`隐藏了。

设计模式GitHub仓库地址：[https://github.com/QinJiangbo/DesignPatterns](https://github.com/QinJiangbo/DesignPatterns)
