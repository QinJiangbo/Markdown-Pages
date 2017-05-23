---
title: Guava优美代码-6-Throwables
date: 2016-10-21 19:15:26
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Throwables
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Throwables
---
## 简化异常处理的Throwables
Guava提供了一个异常处理工具类, 可以简单地捕获和重新抛出多个异常。这个工具就是Throwables。借助Throwables工具类，我们可以很轻松地完成以下一些事情：

- 获取异常链
- 获取最底层的异常
- 过滤异常，只抛出我们感兴趣的异常
- 把受检查的异常转换为运行时异常

下面直接给出上述几种用例场景的示例代码分析。

### 获取异常链(getCausalChain)

``` java
@Test
public void testThrowablesGetCausalChain() {
    try {
        try {
            try {
                throw new RuntimeException("Inner Most exception");
            } catch (Exception e) {
                throw new SQLException("Middle tier exception", e);
            }
        } catch (Exception e) {
            throw new IllegalStateException("Last exception", e);
        }
    } catch (Exception e) {
        System.out.println(Throwables.getCausalChain(e));
    }
}
```
上述代码会打印出三个try语句的抛出异常集合。

### 获取最底层的异常(getRootCause)

``` java
@Test
public void testThrowablesGetRootCause() {
    try {
        try {
            try {
                throw new RuntimeException("Inner Most exception");
            } catch (Exception e) {
                throw new SQLException("Middle tier exception", e);
            }
        } catch (Exception e) {
            throw new IllegalStateException("Last exception", e);
        }
    } catch (Exception e) {
        System.out.println(Throwables.getRootCause(e).getMessage());
    }
}
```
这段代码会打印出`Inner Most exception`，因为它是嵌套在最里面的异常，是所有异常抛出的最根本的原因。

### 过滤异常(propagrateIfInstanceOf)

``` java
@Test
public void testPropagrateIfInstanceOf() throws IOException {
    try {
        throw new IOException();
    } catch (Throwable t) {
        Throwables.propagateIfInstanceOf(t, NullPointerException.class);
        System.out.println("Hello World!");
    }
}
```
这段代码会直接打印`Hello World!`，因为抛出的异常是`IOException`，而我们感兴趣的是空指针异常`NullPointerException`。

### 把受检查的异常转换为运行时异常(propagate)

``` java
@Test
public void testThrowablesPropagate() {
    try {
        throw new Exception();
    } catch (Throwable t) {
        String messages = Throwables.getStackTraceAsString(t);
        System.out.println("messages: " + messages);
        Throwables.propagate(t); // RuntimeException
    }
}
```
这个方法将以这个普通的类转化为了运行时异常。

## 总结
以上就是关于Guava的几种常见的用法，当然了，关于Guava的使用也需要仔细斟酌，如果使用了Guava的这个Throwables工具类之后，导致整体的代码变得很复杂难以理解，那么博主劝你赶紧放弃使用这个工具类，因为对于服务器而言，安全和稳定优先！