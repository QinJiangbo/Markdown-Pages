---
title: SingleLinkedList独立实现
date: 2016-07-16 23:28:43
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/linkedlist-single-thumbnail.png
tags:
	- 数据结构
	- 单向链表
categories:
	- 架构师
	- 算法
keywords:
	- 数据结构
	- 单向链表
---
![](https://obrxbqjbi.qnssl.com/blog/image/linkedlist-single-thumbnail.png)

现在对Java原生数据结构特感兴趣，于是决定自己动手实现一些类，比如这个List类，在熟习了单链表和双链表的数据结构之后，终于实现了，代码如下：

``` java
package hk.inso.service;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/10/15 6:09 PM
 * Author: Richard
 */
public class SingleLinkedList<E> {

    private static class Node<E> {

        private E element;
        private Node next;

        public Node(E element) {
            //constructor with args
            this.element = element;
        }

        public Node() {
            //constructor without args
        }

        @Override
        public String toString() {
            return element.toString();
        }
    }

    private Node first;
    private Node last;

    /**
     * 添加元素到链表头部
     * @param element
     */
    public void add(E element) {
        Node<E> node = new Node<E>(element);
        linkFirst(node);
    }
    /**
     * 添加元素到链表尾部
     * @param element
     */
    public void addLast(E element) {
        Node<E> node = new Node<E>(element);
        linkLast(node);
    }

    /**
     * 根据索引移除元素
     * @param index
     */
    public void remove(int index) {
        if (index == 0) {
            unlinkFirst();
        }
        else{
            Node node = getNode(index);
            unlink(node);
        }

    }

    /**
     * 移除链表中最后一个元素
     */
    public void removeLast() {
        unlinkLast();
    }

    /**
     * 将元素插入指定链表索引为止
     * @param index
     * @param element
     */
    public void insert(int index, E element) {
        Node<E> node1 = new Node<E>(element);
        if(index == 0) {
            linkFirst(node1);
        } else {
            Node node2 = getNode(index);
            link(node1, node2);
        }
    }

    /**
     * 获取某个元素
     * @param index
     * @return
     */
    public E get(int index) {
        Node<E> node = getNode(index);
        E element = node.element;
        return element;
    }

    /**
     * 统计链表里面所有元素的个数
     * @return 总的元素的个数
     */
    public int size() {
        int count = 0;
        Node node = first;
        while (node != null) {
            count++;
            node = node.next;
        }
        return count;
    }

    /**
     * toString方法实现
     * @return
     */
    @Override
    public String toString() {
        Node node = first;
        StringBuilder stringBuilder = new StringBuilder();
        if(node == null) {
            return "{}";
        }
        while (node != null) {
            stringBuilder.append("[" + node.element.toString() +"],");
            node = node.next;
        }
        String result = stringBuilder.toString();
        int index = result.lastIndexOf(",");
        return result.substring(0, index);

    }

    /**
     * 插入结点到链表中间
     * 插入前
     * node2->node3->node4
     * 插入后
     * node2->node1->node3->node4
     * @param node1 要插入的结点
     * @param node2 插入结点位置结点
     */
    private void link(Node node1, Node node2) {
        node1.next = node2;
        Node prevNode = getPrevNode(node2);
        prevNode.next = node1;
    }

    /**
     * 插入结点到链表首部
     * @param node
     */
    private void linkFirst(Node node) {

        if(first == null || last == null) {
            first = last = node;
        } else {
            node.next = first;
            first = node;
        }

    }

    /**
     * 插入结点到链表尾部
     * @param node
     */
    private void linkLast(Node node) {
        last.next = node;
        last = node;
    }

    /**
     * 从链表中间移除当前结点
     * @param node
     */
    private void unlink(Node node) {
        Node prevNode = getPrevNode(node);
        prevNode.next = node.next;
    }

    /**
     * 移除头部的结点
     */
    private void unlinkFirst() {
        first = first.next;
    }

    /**
     * 移除尾部的结点
     */
    private void unlinkLast() {
        Node prevNode = getPrevNode(last);
        prevNode.next = null;
        last = prevNode;
    }

    /**
     * 寻找当前结点的前一个结点
     * @param node 当前结点
     * @return
     */
    private Node getPrevNode(Node node) {
        //这里多走了一步
        // Node nodeItr = first.next;
        Node nodeItr = first;
        Node prevNode = new Node();
        while (!nodeItr.equals(node)) {
            prevNode = nodeItr;
            nodeItr = nodeItr.next;
        }
        return prevNode;
    }

    /**
     * 根据索引查找链表中得数据
     * @param index
     * @return 链表结点
     */
    private Node getNode(int index) {
        Node node = first;
        Node currentNode = new Node();
        if (index == 0) {
            //continue
        }

        if (index >= size()) {
            throw new IndexOutOfBoundsException(errMessage(index));
        }
        else {
            int i = 0;
            while (i <= index) {
                if(node != null) {
                    currentNode = node;
                    //注意这里有递增
                    node = node.next;
                }
                i++;
            }
        }
        return currentNode;
    }

    /**
     * 错误消息显示
     * @param index
     * @return
     */
    private String errMessage(int index) {
        return "Size: "+ size() + ", index: "+ index;
    }

}
```

测试代码:

``` java
package hk.inso.test;

import hk.inso.service.SingleLinkedList;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/11/15 4:58 PM
 * Author: Richard
 */
public class TestSingleLinkedList {

    public static void main(String[] args) {

        SingleLinkedList<User> list = new SingleLinkedList<User>();
        list.add(new User(5, "Jimmy", "Girl"));
        list.add(new User(7, "Tom", "Boy"));
        list.addLast(new User(6, "Amy", "Girl"));
        System.out.println(list.toString());
    }
}

class User {

    private int age;
    private String name;
    private String gender;

    public User(int age, String name, String gender) {
        this.age = age;
        this.name = name;
        this.gender = gender;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    @Override
    public String toString() {
        return "User{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                '}';
    }
}
```

以上代码均测试通过！