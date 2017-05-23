---
title: Java编程思想之初始化顺序（七）
date: 2017-05-15 19:36:50
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/ThinkingInJava.png
tags:
	- Java
	- 编程思想
categories:
	- 开发技术
	- Java
keywords:
	- Java
	- 编程思想
---
Java面试中经常考查同学们的一个问题就是**对象的初始化顺序**。本文就重点说一说Java中的类和对象的初始化顺序。

## 初始化顺序
我们都知道，在类的内部，变量定义的先后顺序决定了初始化的顺序。即使变量定义散布于方法定义之间，它们仍旧会在任何方法（包括构造器）被调用之前得到初始化。看看下面这个例子👇：

``` java
class Window {
	Window(int marker) { 
	 	System.out.println("Window(" + marker + ")");
	 }
}

class House {
	Window w1 = new Window(1);
	House() {
		System.out.println("House()");
		w3 = new Window(33);
	}
	Window w2 = new Window(2);
	
	void f() {
		System.out.println("f()");
	}
	Window w3 = new Window(3);
}

public class OrderOfInitialization {
	public static void main(String[] args) {
		House h = new House();
		h.f();
	}
}
```
输出结果如下：

``` bash
Window(1)
Window(2)
Window(3)
House()
Window(33)
f()
```
从输出的结果大家可以注意到这样一个事实，就是对象`w3`被初始化了两次。在`House`类中，故意将几个`Window`对象的定义散布到各处，以证明它们全部都会在调用构造器或者其他方法之前得到初始化。另外，我们也知道对象`w3`后来又在构造器内被初始化了。

## 静态数据的初始化
无论创建多少个对象，**静态数据都只占用一份存储区域**。`static`关键字不能应用于局部变量，因此它只能作用于域。如果一个域是静态的基本类型，并且没有对它进行初始化，那么它就会获得基本类型的标准初值；如果它是一个对象引用，那么它的默认初始化值就是`null`。看看下面这个例子：

``` java
public class LoadingOrder {

    public static void main(String[] args) {
        new Parent(13);
        System.out.println(Parent.age);
    }
}

class Parent {
    static int age;
    static Child child1 = new Child(1);
    Child child3 = new Child(3);
    Parent(int id){
        System.out.println("Parent(" + id + ")");
    }
    static Child child2 = new Child(2);
}

class Child {

    Child(int id) {
        System.out.println("Child(" + id + ")");
    }
}
```
输出结果如下：

``` bash
Child(1)
Child(2)
Child(3)
Parent(13)
0
```
为什么会是这个结果呢？我们可以看到，在`Parent`类中，我们按照先后顺序定义了`age`，`child1`，`child3`，`child2`等域。其中`child3`是非静态域。输出的结果是先输出了`Child(1)`，然后是`Child(2)`，接着是`Child(3)`。注意这儿，这里说明了一个事实，就是**静态域的初始化早于非静态域**。然后输出`Parent(13)`，说明**非静态域的初始化又早于构造函数**。最后一个输出`0`印证了我们前面说的基本数据类型会取相关的初值。

总结一下对象创建的过程，假设有一个类叫`People`类：

1. 即使没有显示地使用`static`关键字，构造器实际上也是静态方法。因此，当首次创建类型为`People`的对象时（构造器可以看成是静态方法），或者`People`类的静态方法/静态域首次被访问时，Java解释器必须查找类路径，以定位**People.class**文件。
2. 然后载入**People.class**，这将会在堆中创建一个`Class`对象，有关静态初始化的所有动作都会被执行。因此，静态初始化只有在**Class**对象首次加载的时候才会执行一次。
3. 当使用`new People()`创建对象的时候，首先在堆上为`People`对象分配足够的存储空间，关于存储空间到底多大，这是根据`People`类中的数据结构决定的。
4. 这块存储空间会被清零，这就自动地将`People`对象中的所有基本类型的数据都设置成了默认值（对于数值型的就是0，布尔型为`false`（底层是0），字符型为0），而引用则被设置为了`null`。
5. 执行所有出现于字段定义出的初始化动作。
6. 执行构造器。后面与继承结合起来的时候情况更加复杂。


## 子类父类静态块初始化顺序
关于这部分内容，我建议大家移步到我的另一篇博文。
[static静态代码块，静态变量等加载顺序研究](http://qinjiangbo.com/static-block-load-ordering.html)
