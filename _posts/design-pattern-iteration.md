---
title: 设计模式学习之迭代子模式
date: 2017-04-06 12:53:19
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/iterator-pattern.png
tags:
	- 迭代子模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 迭代子模式
	- 设计模式
---
## 什么是迭代子模式
**迭代子模式(Iterator Design Pattern)**`提供了一种顺序访问聚合对象中的元素但是又不暴露它的底层实现的方式。`

## 简单补充
迭代子模式就是分离了集合对象的遍历行为，抽象出一个迭代器类来负责，这样既可以做到不暴露集合的内部结构，又可让外部代码透明地访问集合内部的数据。大家肯定都使用过迭代器去遍历一个集合中的元素吧？比如像下面这样遍历链表：

``` java
List<Integer> list = new ArrayList<>();
Iterator iterator = list.iterator();
while(iterator.hasNext()) {
	// do something
}
```
我们并不知道这个迭代器是如何遍历这个链表的，但是它就做到了，那么本文就来聊聊如何实现这个迭代器。

## 迭代子模式类图
我们先看看迭代子模式的类图：
![迭代子模式](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/iterator-01.png)

我们先看`Client`客户类，客户类没啥存在感，算了不说它了，换一个。

看看`Aggregate`接口，这是一个聚合对象接口。它提供了一个通用的聚合对象接口，将你的客户类和聚合对象底层的实现分离开了。

`ConcreteAggregate`类是`Aggregate`接口的具体实现类。我们可以看到，里面具有一个`createIterator()`方法，这个方法会创造一个`Iterator`对象。然后通过这个对象遍历聚合对象的各个元素。

`Iterator`接口是一个迭代器接口，它定义了三个方法：`hasNext()`，`next()`以及`remove()`。这三个方法非常的重要，第一个方法`hasNext()`会判断还有没有下一个元素，避免迭代器遍历越界。第二个方法`next()`会返回下一个将要遍历的元素。第三个方法`remove()`会移除当前遍历的元素。通过这三个方法，我们能够对集合做一些基本的操作。这里本文使用的是`java.util.Iterator`，也就是JDK中的`Iterator`接口，如果你不想用，也可以自己实现。

`ConcreteIterator`类是`Iterator`接口的具体实现类，它在`ConcreteAggregate`类里面被引用。作为`createIterator()`方法的返回值。

## 实例分析-厨师菜单
假设我们现在有一个需求就是有两个厨师使用了不同的方式来实现了一个菜单，这个菜单包含很多不同的菜名，一个用的是数组元素（`ArrayList`），一个用的是哈希表元素（`HashTable`）。我们分别将这两个命名为`DinerMenu`和`PancakeMenu`。根据我们的迭代子设计模式类图，我们知道，他们都必须实现一个聚合类，那我们就定义这个接口为Menu吧。接下来为这两种不同的实现分别定义他们各自的遍历迭代器。

## 代码实现
Menu.java

``` java
public interface Menu {
    Iterator createMenuIterator();
}
```
MenuItem.java

``` java
public class MenuItem {

    private String name;
    private String description;
    private boolean vegetarian;
    private double price;

    public MenuItem(String name, String description,
                    boolean vegetarian, double price) {
        this.name = name;
        this.description = description;
        this.vegetarian = vegetarian;
        this.price = price;
    }

    // set methods
}
```
PancakeMenu.java

``` java
public class PancakeMenu implements Menu {

    ArrayList menuItems;

    public PancakeMenu() {
        menuItems = new ArrayList();

        addItem("K&B's Pancake Breakfast",
                "Pancakes with scrambled eggs, and toast",
                true,
                2.99);

        addItem("Regular Pancake Breakfast",
                "Pancakes with fried eggs, sausage",
                false,
                2.99);

        addItem("Blueberry Pancake",
                "Pancakes made with fresh blueberries",
                true,
                3.49);

        addItem("Waffles",
                "Waffles, with your choices of blueberries or strawberries",
                true,
                3.59);

    }

    public void addItem(String name, String description,
                        boolean vegetarian, double price) {
        MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
        menuItems.add(menuItem);
    }

    @Override
    public Iterator createMenuIterator() {
        return new PancakeMenuIterator(menuItems);
    }
}
```
DinerMenu.java

``` java
public class DinerMenu implements Menu {

    private static final int MAX_SIZE = 6;
    private int numberOfItems = 0;
    private MenuItem[] menuItems;

    public DinerMenu() {
        menuItems = new MenuItem[MAX_SIZE];

        addItem("Vegetarian BLT",
                "(Fakin) Bacon with lettuce & tomato on the whole wheat",
                true,
                2.99);

        addItem("BLT",
                "Bacon with lettuce & tomato on the whole wheat",
                false,
                2.99);

        addItem("Soup of the day",
                "Soup of the day, with a side of potato salad",
                false,
                3.29);

        addItem("Hotdog",
                "A hot dog",
                false,
                3.05);
    }

    public void addItem(String name, String description,
                        boolean vegetarian, double price) {
        MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
        if (numberOfItems >= MAX_SIZE) {
            System.err.println("Sorry, menu is full! Can't add items!");
        } else {
            menuItems[numberOfItems++] = menuItem;
        }
    }

    @Override
    public Iterator createMenuIterator() {
        return new DinerMenuIterator(menuItems);
    }
}
```

接下来定义相关的迭代器类：

PancakeMenuIterator.java

``` java
public class PancakeMenuIterator implements Iterator {

    ArrayList menuItems;
    int position = 0;

    public PancakeMenuIterator(ArrayList menuItems) {
        this.menuItems = menuItems;
    }

    @Override
    public boolean hasNext() {
        return position < menuItems.size()
                && menuItems.get(position) != null;
    }

    @Override
    public Object next() {
        MenuItem menuItem = (MenuItem) menuItems.get(position++);
        return menuItem;
    }
}
```

DinerMenuIterator.java

``` java
public class DinerMenuIterator implements Iterator {

    MenuItem[] menuItems;
    int position = 0;

    public DinerMenuIterator(MenuItem[] menuItems) {
        this.menuItems = menuItems;
    }

    @Override
    public boolean hasNext() {
        return position < menuItems.length
                && menuItems[position] != null;
    }

    @Override
    public Object next() {
        MenuItem menuItem = menuItems[position++];
        return menuItem;
    }

    @Override
    public void remove() {
        if (position <= 0) {
            throw new IllegalStateException
                ("you can' remove an item until you've done at least one next()!");
        }
        if (menuItems[position - 1] != null) {
            for (int i = position - 1; i < (menuItems.length - 1); i++) {
                menuItems[i] = menuItems[i + 1];
            }
            menuItems[menuItems.length - 1] = null;
        }
    }
}
```

### 测试代码
Waitress.java

``` java
public class Waitress {

    DinerMenu dinerMenu;
    PancakeMenu pancakeMenu;
    CafeMenu cafeMenu;

    public Waitress(DinerMenu dinerMenu, PancakeMenu pancakeMenu) {
        this.dinerMenu = dinerMenu;
        this.pancakeMenu = pancakeMenu;
    }

    public void printMenu() {
        DinerMenuIterator dinerMenuIterator =
                (DinerMenuIterator) dinerMenu.createMenuIterator();
        Iterator pancakeMenuIterator =
                pancakeMenu.createMenuIterator();
        System.out.println("MENU\n----\nBREAKFAST");
        printMenu(dinerMenuIterator);
        System.out.println("\nDINER");
        printMenu(pancakeMenuIterator);

    }

    private void printMenu(Iterator iterator) {
        while (iterator.hasNext()) {
            MenuItem menuItem = (MenuItem) iterator.next();
            System.out.print(menuItem.getName() + ", ");
            System.out.print(menuItem.getDescription() + " -- ");
            System.out.println(menuItem.getPrice());
        }
    }
}
```

MenuTest.java

``` java
public class MenuTest {
    public static void main(String[] args) {
        DinerMenu dinerMenu = new DinerMenu();
        PancakeMenu pancakeMenu = new PancakeMenu();
        Waitress waitress = new Waitress(dinerMenu, pancakeMenu);
        waitress.printMenu();
    }
}
```

## 总结
说说JAVA中的`iterator`应用。JAVA中的`java.util.Iterator`接口为我们提供了很多默认方法的实现，为我们实现迭代子模式带来了巨大的便利。我们来看看迭代子模式有哪些优点和缺点：

### 优点
 1. 它支持以不同的方式遍历一个聚合对象。 
 2. 迭代器简化了聚合类。 
 3. 在同一个聚合上可以有多个遍历。
 4. 在迭代器模式中，增加新的聚合类和迭代器类都很方便，无须修改原有代码。

### 缺点
由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。