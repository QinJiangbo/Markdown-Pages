---
title: LinkedList实现基于LRU算法的缓存
date: 2016-07-13 23:38:03
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/lru-thumbnail.png
tags:
	- 数据结构
	- LRU
categories:
	- 架构师
	- 算法
keywords:
	- 数据结构
	- LRU
---
![](https://obrxbqjbi.qnssl.com/blog/image/lru-thumbnail.png)

学过操作系统的人都知道LRU页面切换算法，其实这个算法不仅仅只是能在页面切换中应用到，在缓存中也有很实际的应用。最典型的实现方式是采用LinkedHashMap来实现这个缓存，大家可以在Java源码里面看到这个类的作者关于这个的描述，不过全是英文，但是却明确提到过。

下面废话不多说，直接展示我自己关于这个算法实现的代码吧，亲测通过：

核心算法代码：

``` java
import java.util.Hashtable;
import java.util.LinkedList;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/7/15 4:46 PM
 * Author: Richard
 */
public class LinkedListCache<Object>{

    //默认的缓存大小
    private static int CAPACITY = 0;

    //引用一个双向链接表
    private LinkedList<Object> list;

    //构造函数
    public LinkedListCache(int capacity) {
        this.CAPACITY = capacity;
        list = new LinkedList<Object>();
    }

    //添加一个元素
    public synchronized void put(Object object) {

        if(list != null && list.contains(object)) {
            list.remove(object);
        }
        removeLeastVisitElement();
        list.addFirst(object);
    }

    //移除最近访问次数最少的元素
    private synchronized void removeLeastVisitElement() {

        int size = size();

        //注意，这儿必须得是CAPACITY - 1否则所获的size比原来大1
        if(size > (CAPACITY - 1) ) {
            Object object = list.removeLast();
            System.out.println("本次被踢掉的元素是:" + object.toString());
        }
    }

    //获取第N个索引下面的元素
    public synchronized Object get(int index) {
        return list.get(index);
    }

    //清空缓存
    public synchronized void clear() {
        list.clear();
    }

    //获取链接表的大小
    public int size() {
        if(list == null) {
            return 0;
        }
        return list.size();
    }

    //toString方法
    public String toString() {
        return list.toString();
    }

}
```

测试代码：

``` java
package hk.inso.www.test;

import hk.inso.www.cache.LRUCache;
import hk.inso.www.cache.LinkedListCache;
import hk.inso.www.cache.MapCache;

import java.util.Iterator;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;

/**
 * Created by Richard on 8/5/15.
 */
public class CacheTest {

    public static void main(String[] args) throws InterruptedException {
        
        LinkedListCache linkedListCache = new LinkedListCache<String>(5);

        linkedListCache.put("1");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("2");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("3");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("4");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("5");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("1");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("6");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("4");
        System.out.println(linkedListCache.toString());
        Thread.sleep(1000);
        linkedListCache.put("7");
        System.out.println(linkedListCache.toString());
    }
}
```

不要吐槽这个哈，时间关系，就直接这么演示了，你可以直接拷贝下来运行就可以了。希望可以帮到你！