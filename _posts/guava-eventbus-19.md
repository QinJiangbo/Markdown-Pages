---
title: Guava优美代码-19-EventBus
date: 2016-11-07 01:10:27
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- EventBus
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- EventBus
---
## Guava事件总线
EventBus是Guava的事件处理机制，是设计模式中的观察者模式（生产/消费者编程模型）的优雅实现。对于**事件监听**和**发布订阅**模式，EventBus是一个非常优雅和简单解决方案，我们不用创建复杂的类和接口层次结构。

对于观察者模式，我这里就不再赘述了，大家可以去我的另一篇博文[《设计模式学习之观察者模式(Observer)》](http://t.cn/RfhLCvE)里面详细了解观察者模式。

## EventBus使用实例

### 单事件监听

消息监听

``` java
package com.qinjiangbo.service;

import com.google.common.eventbus.Subscribe;

/**
 * Date: 9/20/16
 * Author: qinjiangbo@github.io
 */
public class EventListener {

    public int lastMessage = 0;

    @Subscribe
    public void listen(OurTestEvent event) {
        lastMessage = event.getMessage();
    }

    public int getLastMessage() {
        return lastMessage;
    }
}
```
消息发送

``` java
package com.qinjiangbo.service;

/**
 * Date: 9/20/16
 * Author: qinjiangbo@github.io
 */
public class OurTestEvent {

    private final int message;

    public OurTestEvent(int message) {
        this.message = message;
    }

    public int getMessage() {
        return message;
    }
}
```

测试类

``` java
@Test
public void testReceiveEvent() {
    EventBus eventBus = new EventBus("test");
    EventListener eventListener = new EventListener();
    eventBus.register(eventListener);
    eventBus.post(new OurTestEvent(200));
    System.out.println(eventListener.getLastMessage());
} // 200
```

### 多事件监听

多事件监听类

``` java
package com.qinjiangbo.service;

import com.google.common.eventbus.Subscribe;

/**
 * Date: 9/20/16
 * Author: qinjiangbo@github.io
 */
public class MultipleListener {

    public Integer lastInteger;
    public Long lastLong;

    @Subscribe
    public void listenInteger(Integer event) {
        lastInteger = event;
    }

    @Subscribe
    public void listenLong(Long event) {
        lastLong = event;
    }

    public Integer getLastInteger() {
        return lastInteger;
    }

    public Long getLastLong() {
        return lastLong;
    }
}

```

测试类

``` java
@Test
public void testReceiveMultipleEvents() {
    EventBus eventBus = new EventBus("test");
    MultipleListener multipleListener = new MultipleListener();
    eventBus.register(multipleListener);
    eventBus.post(new Integer(100));
    eventBus.post(new Long(200L));
    System.out.println(multipleListener.getLastInteger()); // 100
    System.out.println(multipleListener.getLastLong()); // 200
}
```

## 总结
### 事件监听者[Listeners]

#### 监听特定事件（如，CustomerChangeEvent）：

- 传统实现：定义相应的事件监听者类，如`CustomerChangeEventListener`；
- EventBus实现：以`CustomerChangeEvent`为唯一参数创建方法，并用`Subscribe`注解标记。

#### 把事件监听者注册到事件生产者：

- 传统实现：调用事件生产者的`registerCustomerChangeEventListener`方法；这些方法很少定义在公共接口中，因此开发者必须知道所有事件生产者的类型，才能正确地注册监听者；
- EventBus实现：在EventBus实例上调用`EventBus.register(Object)`方法；请保证事件生产者和监听者共享相同的EventBus实例。

#### 按事件超类监听（如，EventObject甚至Object）：

- 传统实现：很困难，需要开发者自己去实现匹配逻辑；
- EventBus实现：EventBus自动把事件分发给事件超类的监听者，并且允许监听者声明监听接口类型和泛型的通配符类型（wildcard，如 `? super XXX`）。

#### 检测没有监听者的事件：

- 传统实现：在每个事件分发方法中添加逻辑代码（也可能适用AOP）；
- EventBus实现：监听`DeadEvent`；EventBus会把所有发布后没有监听者处理的事件包装为DeadEvent（对调试很便利）。

### 事件生产者[Producers]

#### 管理和追踪监听者：

- 传统实现：用列表管理监听者，还要考虑线程同步；或者使用工具类，如`EventListenerList`；
- EventBus实现：EventBus内部已经实现了监听者管理。

#### 向监听者分发事件：

- 传统实现：开发者自己写代码，包括事件类型匹配、异常处理、异步分发；
- EventBus实现：把事件传递给`EventBus.post(Object)`方法。异步分发可以直接用EventBus的子类`AsyncEventBus`。

