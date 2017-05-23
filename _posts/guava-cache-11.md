---
title: Guava优美代码-11-Cache
date: 2016-10-31 22:57:46
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Cache
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Cache
---
## Guava缓存Cache
缓存（Cache）是我们在开发中为了提高系统的性能，把经常的访问业务数据在第一次访问时没有就先处理结果然后放到缓存中，第二次就不用在对相同的业务数据再重新处理一遍，这样就大大提高了系统的性能。缓存被广泛地引用在各个企业的业务系统中，Guava为我们提供了一个非常好的缓存架构，下面介绍Guava Cache的具体使用方法。

## 缓存分类
缓存分好几种：

- 本地缓存。
- 数据库缓存。
- 分布式缓存。

**本地缓存**目前使用比较广泛的有EhCache，这是一个完全基于本地内存的缓存框架，可以做成分布式，也可以做成单机模式。**分布式缓存**比较常用的有`memcached`， `redis`等，`memcached`是高性能的分布式内存缓存服务器，缓存业务处理结果，减少数据库访问次数和相同复杂逻辑处理的时间，以提高动态Web应用的速度、 提高可扩展性。

## Guava缓存之LoadingCache
LoadingCache是附带CacheLoader构建而成的缓存实现。创建自己的CacheLoader通常只需要简单地实现V load(K key) throws Exception方法。

``` java
@Test
public void testLoadingCache() throws ExecutionException {
    Map<String, String> database = Maps.newHashMap();
    database.put("richard", "That's my way!");
    database.put("amy", "I love you!");
    database.put("nike", "Just do it!");
    LoadingCache<String, String> cache = CacheBuilder
            .newBuilder()
            .build(new CacheLoader<String, String>() {
                @Override
                public String load(String key) throws Exception {
                    return database.get(key);
                }
            });
    cache.put("whu", "WuHan University");
    System.out.println(cache.get("whu"));
}
```

注意上面代码中，`CacheLoader`这个类的内部定义了一个`load(String key)`方法，专门用来加载数据，我们一般会在这个地方写上具体的业务逻辑，比如这里的参数key可以是我们的一个业务编号id，通过这个id我们可以查询企业相关的业务信息，然后将业务信息缓存起来，下次再来取的时候就直接从缓存中读取了。这样就极大地提升了系统的响应速度！

## Guava缓存之CallableCache
所有类型的Guava Cache，不管有没有自动加载功能，都支持`get(K, Callable<V>)`方法。这个方法返回缓存中相应的值，或者用给定的`Callable`运算并把结果加入到缓存中。Callable只有在缓存值不存在或者为空时，才会调用。

在整个加载方法完成前，缓存项相关的可观察状态都不会更改。这个方法简便地实现了模式"如果有缓存则返回；否则进行相关逻辑运、缓存、然后返回"

``` java
@Test
public void testCallableCache() throws ExecutionException {
    Cache<String, String> cache = CacheBuilder
            .newBuilder()
            .maximumSize(1000)
            .build();
    //Callable只有在缓存值不存在或者为空时，才会调用
    String retriValue = cache.get("name", new Callable<String>() {
        @Override
        public String call() throws Exception {
            return "name ==> qinjiangbo";
        }
    });
    System.out.println(retriValue);
    cache.put("whu", "WuHan University");
    String retriValue2 = cache.get("whu", new Callable<String>() {
        @Override
        public String call() throws Exception {
            return "name ==> whu";
        }
    });
    System.out.println(retriValue2);
}
```

注意到上面的`get`方法，当缓存去调用这个`get`方法的时候，必须指定一个`Callable`方法，以防止未命中时可以进行相应的逻辑处理。

可以这么来理解，就是说我们想通过缓存区读取业务系统的业务信息，然后未命中，说明不在缓存对吧，这个时候就需要有一个逻辑去处理未命中的事件，其实就是我们需要去写一个函数拿着这个未命中的`key`取数据库读取相应的业务信息，然后缓存并返回给用户。

或者我们说这个地方也可以设置成缓存用户的在线信息`ticket`，当这个用户的`ticket`有效时，我们可以直接读取，当这个用户信息无效时，可以通过这个`Callable`调用另一个方法比如`MD5`加密方法对用户信息加密并生成新的`ticket`。

## 总结
上面只是写了Guava缓存中两个最常见的缓存实现方式，并且也只是简单地做了一个测试，具体的缓存设置需要大家根据自己业务情况进行相应的设置。在CacheBuilder实例后面通过`.`号可以触发方法，读者可以很清楚地根据设置自己的缓存。

