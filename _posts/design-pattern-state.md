---
title: 设计模式学习之状态模式
date: 2017-04-09 15:39:53
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/state-pattern.png
tags:
	- 状态模式
	- 设计模式
categories:
	- 设计模式
keywords:
	- 状态模式
	- 设计模式
---
## 什么是状态模式
**状态模式(State Design Pattern)**`允许对象在内部状态改变的时候改变它的行为，对象看起来好像修改了它的类。`

**`说明`**在状态模式中，我们会创建表示各种状态的对象和一个行为随着状态对象变化而改变的`Context`对象。

## 状态模式类图
![状态模式类图](https://obrxbqjbi.qnssl.com/blog/image/desgin-patterns/state-01.png)

下面具体介绍介绍各个类的作用：

`Context`是我们前面说的一个上下文类，它拥有一些内部状态（`state`）。在类图中，只要有人调用`Context`类的`request()`方法，它就会被委托到`state`的`handle()`方法中来处理。

`State`接口定义了一个所有具体状态的共同接口，任何状态都会实现这个相同的接口，这样一来，各个状态之间就可以相互替换。

`ConcreteState`具体状态类处理来自`Context`类的请求。每一个`ConcreteState`都提供了自己对于这个请求的具体实现。所以，当`Context`状态改变的时候，它的行为也一定会跟着改变。

## 实例分析-ATM取钱
我们假设有一台ATM机摆在我们面前，这个ATM的功能非常简单，就是取钱！不过呢，为了使代码更加简化，便于我们的模式的学习，我将这个ATM机的功能简化了，每次只能取1000整数。而且不能存款。先说明需要使用到的几个类：

`ATM`类，这个类就是我们上面分析的`Context`上下文类，它里面保存了一个`State`状态类实例，这个实例会根据数据库（`DB`）余额的大小动态地发生变化。

`DB`类，这里主要是模拟数据库请求。在状态类中执行相关的操作的时候就会直接和数据库类`DB`发生关联。

`State`接口是所有状态类的接口，它有两个具体实现类`SufficientState`和`DefficientState`类。分别表示数据库余额充足和余额不足的情况。

`Client`客户类是我们的一个测试类。

## 代码实现

ATM.java（Context）

``` java
/**
 * @date: 09/04/2017 4:28 PM
 * @author: qinjiangbo@github.io
 */
public class ATM {
    private State state;

    public ATM() {
        state = new SufficientState(this);
    }

    public void withdraw() {
        state.handle();
    }

    public void setState(State state) {
        this.state = state;
    }

}
```

DB.java

``` java
/**
 * @date: 09/04/2017 4:52 PM
 * @author: qinjiangbo@github.io
 */
public class DB {
    private static int total = 10000;

    public static int take(int num) {
        total = total - num;
        return total;
    }

    public static int query() {
        return total;
    }

}
```

State.java

``` java
/**
 * @date: 09/04/2017 4:32 PM
 * @author: qinjiangbo@github.io
 */
public interface State {
    void handle();
}
```

SufficientState.java

``` java
/**
 * @date: 09/04/2017 4:36 PM
 * @author: qinjiangbo@github.io
 */
public class SufficientState implements State {
    private ATM atm;

    public SufficientState(ATM atm) {
        this.atm = atm;
    }

    @Override
    public void handle() {
        int count = DB.take(1000);
        if (count == 0) {
            atm.setState(new DefficientState(atm));
        }
        System.out.println("$1000 HAVE BEEN TAKEN!");
    }
}
```

DefficientState.java

``` java
/**
 * @date: 09/04/2017 4:37 PM
 * @author: qinjiangbo@github.io
 */
public class DefficientState implements State {
    private ATM atm;

    public DefficientState(ATM atm) {
        this.atm = atm;
    }

    @Override
    public void handle() {
        if (DB.query() > 0) {
            DB.take(1000);
            atm.setState(new SufficientState(atm));
            System.out.println("$1000 HAVE BEEN TAKEN!");
        } else {
            System.out.println("NOT SUFFICIENT IS AVAILABLE!");
        }
    }
}
```

### 测试

``` java
/**
 * @date: 09/04/2017 4:57 PM
 * @author: qinjiangbo@github.io
 */
public class Client {
    public static void main(String[] args) {
        ATM atm = new ATM();
        // 模拟11次取款行为，每次1000
        for (int i = 0; i < 11; i++) {
            atm.withdraw();
        }
    }
}
```

### 结果

``` bash
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
$1000 HAVE BEEN TAKEN!
NOT SUFFICIENT IS AVAILABLE!
```
可以看到，在取了`atm.withdraw()`10次钱之后，银行数据库的钱已经别取光了。因此，在第11次去取的时候提示没有钱可以取了。

## 总结
这里说说使用状态模式的优点和缺点：
### 优点
1. 封装了状态转换规则
2. 枚举可能的状态，在枚举所有的状态之前需要确定有多少种状态
3. 外面的客户端不会感受到内部发生的变化

### 缺点
1. 状态对象会导致系统的对象数量增多
2. 不利于拓展，因为添加一个新的状态类可能会修改大量的相关类。

总的来说，我们要分特定的场合使用特定的设计模式，具体情况具体分析才是最重要的。