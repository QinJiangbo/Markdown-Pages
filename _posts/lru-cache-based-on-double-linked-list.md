---
title: 基于双向链接表和HashMap实现的LRU缓存
date: 2016-07-22 21:20:08
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/lru-thumbnail.png
tags:
	- 数据结构
	- LRU
	- HashMap
categories:
	- 架构师
	- 算法
keywords:
	- 数据结构
	- LRU
	- HashMap
---
一直在做关于缓存方面的研究，今天终于用非LinkedHashMap的方式将其实现了，Mark一下！

``` java
package hk.inso.service;

import java.util.HashMap;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/10/15 3:25 PM
 * Author: Richard
 */
public class LRUCache<K, V> {

    /**
     * 构造一个内部类作为双向链接表的单个元素
     */
    class Entry<K, V> {
        public V value;
        public K key;
        public Entry prev;
        public Entry next;

        public Entry(K key, V value) {
            this.key = key;
            this.value = value;
        }

        public String toString() {
            return "{"+key+":"+value+"}";
        }
    }

    public final int CAPACITY;
    private HashMap<K, Entry<K, V>> hashMap = new HashMap<K, Entry<K, V>>();
    private Entry first = null;
    private Entry last = null;

    public LRUCache(int capacity) {
        this.CAPACITY = capacity;
    }

    /**
     * 往缓存里面添加元素
     * @param key
     * @param value
     */
    public synchronized void put(K key, V value) {

        if(!hashMap.containsKey(key)) {
            if(hashMap.size() >= CAPACITY) {
                hashMap.remove(last.key);
                remove(last);
            }
            Entry entry = new Entry(key, value);
            addToFirst(entry);
            hashMap.put(key, entry);
            return;
        }

        Entry entry = (Entry) hashMap.get(key);
        moveToFirst(entry);
        hashMap.put(key, entry);
    }

    /**
     * 获取相应key对应的值
     * @param key
     * @return
     */
    public synchronized V get(K key) {
        Entry<K, V> entry =  hashMap.get(key);
        if (entry == null) {
            return null;
        }
        moveToFirst(entry);
        return entry.value;
    }

    /**
     * 从双向链表中移除一个元素
     * @param entry
     */
    public synchronized void remove(Entry entry) {
        if(entry.prev != null) {
            //当前节点被移除，那么它的前一个元素的后继就变成了它当前的后继
            entry.prev.next = entry.next;
        }else{
            //如果当前元素是第一个元素，则它的后继变成第一个元素
            first = entry.next;
        }

        if(entry.next != null) {
            //如果当前节点不是最后一个元素，则它的后一个元素的前驱就是它当前的前驱
            entry.next.prev = entry.prev;
        }else{
            //如果当前元素是最后一个元素，则它的前驱变成了最后一个元素
            last = entry.prev;
        }
        //同时也需要将其从HashMap中除掉
        hashMap.remove(entry.key);
    }

    /**
     * 设置当前元素为第一个元素
     * @param entry
     */
    public synchronized void moveToFirst(Entry entry) {

        if(entry.prev != null) {
            entry.prev.next = entry.next;
        } else {
            //如果该元素为首位，则不作任何改变
            return;
        }

        if(entry.next != null) {
            entry.next.prev = entry.prev;
        } else {
            last = entry.prev;
        }

        entry.next = first;
        first.prev = entry;
        //first只是一个指针，将指针移至第一个
        first = entry;
    }

    /**
     * 添加元素到链表，并将其放在队首
     * @param entry
     */
    public synchronized void addToFirst(Entry entry) {

        if(first == null || last == null) {
            first = last = entry;
            return;
        } else {
            entry.next = first;
            first.prev = entry;
            first = entry;
        }
    }

    @Override
    public String toString() {
        StringBuilder stringBuilder = new StringBuilder();
        Entry entry = first;
        while (entry != null) {
            stringBuilder.append(entry.toString()+" ");
            entry = entry.next;
        }
        return stringBuilder.toString();
    }
}
```