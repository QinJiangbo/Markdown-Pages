---
title: 设计模式学习之组合模式
date: 2017-04-06 16:07:09
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/composite-pattern.png
tags:
	- 组合模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 组合模式
	- 设计模式
---
## 什么是组合模式
**组合模式(Composite Design Pattern)**`允许你将对象组合成树形的结构来表示整体-部分的层级结构。组合模式允许客户类统一对待每一个独立的对象。`

![树形结构](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/composite-01.png)

## 组合模式类图
我们先看看组合模式的类图。
![组合模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/composite-02.png)

先说说`Client`客户类，`Client`客户类利用`Component`接口来操作这个组合结构里面的对象。

`Component`接口定义了这个组合结构里面的所有对象的一个接口，包括组合对象和叶子结点。它定义了四个方法：`operation()`，`add(Component)`，`remove(Component)`以及`getChild(int)`。这四个方法分别对应了相应的操作方法，添加组合对象，移除组合对象以及获取第几个孩子节点。

`Composite`类实现了`Component`接口，组合对象实现了`Component`接口定义的四个方法，但是需要注意的一点就是，组合对象里面可能也实现了叶子结点的相关操作，但是这些操作对于组合对象来说是毫无意义的，所以我们在这些方法里面直接抛出了异常或者做了简单的处理。

`Leaf`类是叶子结点类，它也实现了`Component`接口相关的方法，但是我们从这个类图里面可以看到，它只实现了`operation()`方法，这个方法是为了能和组合对象的操作相兼容而实现的。注意，`Leaf`类并没有相应的孩子节点，它本身就是一个孩子结点。其它的几个方法`add()`，`remove()`以及`getChild()`都不需要实现，因为它根本不支持这些操作。一般的做法就是实现一些钩子（Hook）类来模拟这些操作。但实际上这些钩子中的操作是不会产生实际的作用的。

## 实例分析-员工管理
我们现在需要打印一个企业所有部门的所有员工信息，这个时候我们可以使用组合模式方式来实现相关的操作。首先需要明确一个问题就是员工接口如何设计：

#### Employee
员工类接口，则例说明一下，我们使用抽象类来表示这个员工里面的方法有：

- add(Employee e)
- remove(Employee e)
- getEmployees()
- work()

#### 客户类
这里也就使我们的测试类啦，见下面的源码实现。

## 代码实现

Employee.java

``` java
public abstract class Employee {

    String name;
    String title;
    int deptId;
    List<Employee> employees = new ArrayList<>();

    Employee(String name, String title, int deptId) {
        this.name = name;
        this.title = title;
        this.deptId = deptId;
    }

    abstract void add(Employee e);

    abstract void remove(Employee e);

    void work(){
        System.out.println(this.name + "-" + this.title + "-" + this.deptId);
    }

    abstract List<Employee> getEmployees();

}
```

Manager.java

``` java
public class Manager extends Employee {

    public Manager(String name, String title, int deptId) {
        super(name, title, deptId);
    }

    @Override
    void add(Employee e) {
        employees.add(e);
    }

    @Override
    void remove(Employee e) {
        employees.remove(e);
    }

    @Override
    void work() {
        System.out.println("I am working as manager...");
    }

    @Override
    List<Employee> getEmployees() {
        return employees;
    }
}
```

Staff.java

``` java
public class Staff extends Employee{

    public Staff(String name, String title, int deptId) {
        super(name, title, deptId);
    }

    @Override
    void add(Employee e) {
        throw new UnsupportedOperationException();
    }

    @Override
    void remove(Employee e) {
        throw new UnsupportedOperationException();
    }

    @Override
    void work() {
        System.out.println("I am working as staff...");
    }

    @Override
    List<Employee> getEmployees() {
        return new ArrayList<>();
    }
}
```

Client.java

``` java
public class Client {

    public static void main(String[] args) {
        Employee CEO = new Manager("Richard", "CEO", 1);

        Employee dev1 = new Staff("Lilei", "Java", 1);
        Employee dev2 = new Staff("Leon", "Php", 1);

        Employee COO = new Manager("Amy", "COO", 2);

        Employee ops1 = new Staff("XiXi", "Linux", 2);
        Employee ops2 = new Staff("TiTi", "UNIX", 2);

        CEO.add(dev1);
        CEO.add(dev2);
        CEO.add(COO);

        COO.add(ops1);
        COO.add(ops2);

        for (Employee e: CEO.getEmployees()) {
            e.work();
            for (Employee ee: e.getEmployees()) {
                ee.work();
            }
        }
    }
}
```

## 测试结果
CEO管辖下面的员工信息：

``` bash
Lilei-Java-1
Leon-Php-1
Amy-COO-2
XiXi-Linux-2
TiTi-UNIX-2
```

## 总结
它在我们树型结构的问题中，模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样来处理复杂元素，从而使得客户程序与复杂元素的内部结构解耦。常见的应用场景比如我们的文件管理系统，目录和文件其实是作为一个统一的对象处理的。还有，大型软件里面的复杂的树形菜单，也是采用这种设计模式进行构架的。大家应该自己多看看相关的资料，熟悉熟悉组合模式的使用场景，以便日后在自己的项目当中可以用上派场。