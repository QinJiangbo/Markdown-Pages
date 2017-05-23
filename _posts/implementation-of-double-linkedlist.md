---
title: DoubleLinkedList独立实现
date: 2016-07-18 23:21:50
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/linkedlist-thumbnail.png
tags:
	- 数据结构
	- 双向链表
categories:
	- 架构师
	- 算法
keywords:
	- 数据结构
	- 双向链表
---

![](https://obrxbqjbi.qnssl.com/blog/image/linkedlist-thumbnail.png)

精彩继续，实现完SingleLinkedList之后，今天又实现了双向链接表，代码如下：

``` java
public class DoubleLinkedList<E> {

    class Node<E> {

        E element;
        Node prev;
        Node next;

        public Node(E element) {
            //constructor with args
            this.element = element;
        }

        public Node() {
            //constructor without args
        }

        @Override
        public String toString() {
            return this.element.toString();
        }
    }

    private Node first = null;
    private Node last = null;

    /**
     * 链接结点到链表首部
     * @param node
     */
    private void linkFirst(Node node) {
        if (first == null || last == null) {
            first = last = node;
        }
        node.next = first;
        first.prev = node;
        first = node;
    }

    /**
     * 链接结点到链表尾部
     * @param node
     */
    private void linkLast(Node node) {
        last.next = node;
        node.prev = last;
        last = node;
    }

    /**
     * 链接一个结点到链表中间
     * @param node1 要链接的结点
     * @param node2 链接点位置的结点
     * example:
     *    [插入前]node -> node2 -> node3
     *    [插入后]node -> node1 -> node2 -> node3
     */
    private void link(Node node1, Node node2) {
        Node node = node2.prev;
        node.next = node1;
        node1.prev = node;
        node1.next = node2;
        node2.prev = node1;

    }

    /**
     * 移除某个节点
     * @param node
     */
    public void unlink(Node node) {
        Node prevNode = node.prev;
        Node nextNode = node.next;
        prevNode.next = nextNode;
        nextNode.prev = prevNode;
    }

    /**
     * 移除首结点
     */
    private void unlinkFirst() {
        first = first.next;
        first.prev = null;
    }

    /**
     * 移除尾结点
     */
    private void unlinkLast() {
        last = last.prev;
        last.next = null;
    }

    /**
     * 根据索引获取结点
     * @param index
     * @return
     */
    private Node getNode(int index) {
        Node node = first;
        Node currentNode = new Node();
        if (index == 0) {
            currentNode = first;
        }

        if (index >= size()) {
            throw new IndexOutOfBoundsException(errMessage(index));
        }
        else {
            int i = 0;
            while (i <= index) {
                if (node != null) {
                    currentNode = node;
                    node = node.next;
                }
                i++;
            }
        }
        return currentNode;

    }

    /**
     * 统计链表里面所有元素的个数
     * @return 总的元素的个数
     */
    public int size() {
        Node node = first;
        int count = 0;
        while (node != null) {
            count++;
            node = node.next;
        }
        return count;
    }

    /**
     * 错误消息显示
     * @param index
     * @return
     */
    private String errMessage(int index) {
        return "Size: "+ size() + ", index: "+ index;
    }

    /**
     * 插入元素到链表中
     * @param index
     * @param element
     */
    public void insert(int index, E element) {
        Node node = new Node<E>(element);
        if (index == 0) {
            linkFirst(node);
        }
        if (index < 0 || index >= size()) {
            throw new IndexOutOfBoundsException(errMessage(index));
        }
        if (index == size() -1) {
            linkLast(node);
        }
        else {
            Node node1 = getNode(index);
            link(node, node1);
        }

    }

    /**
     * 添加元素到链表
     * @param element
     */
    public void add(E element) {
        Node node = new Node<E>(element);
        linkFirst(node);
    }

    /**
     * 添加元素到链表首部
     * @param element
     */
    public void addFirst(E element) {
        Node node = new Node<E>(element);
        linkFirst(node);
    }

    /**
     * 添加元素到链表尾部
     * @param element
     */
    public void addLast(E element) {
        Node node = new Node<E>(element);
        linkLast(node);
    }

    /**
     * 移除指定索引下得元素
     * @param index
     */
    public void remove(int index) {
        if (index == 0) {
            unlinkFirst();
        }
        else if (index == size() -1 ){
            unlinkLast();
        }
        else if (index < 0 || index >= size()) {
            throw new IndexOutOfBoundsException(errMessage(index));
        }
        else {
            Node node = getNode(index);
            unlink(node);
        }
    }

    /**
     * 移除最后一个元素
     */
    public void removeLast() {
        unlinkLast();
    }

    /**
     * 移除第一个元素
     */
    public void removeFirst() {
        unlinkFirst();
    }

    /**
     * 清空链表
     */
    public void clear() {
        first = null;
    }

    /**
     * 判断链表是否为空
     * @return
     */
    public boolean isEmpty() {
        return first == null;
    }

    /**
     * 重载toString方法
     * @return
     */
    @Override
    public String toString() {
        Node node = first;
        StringBuilder stringBuilder = new StringBuilder();
        if (node == null) {
            return "{}";
        }
        else {
            stringBuilder.append("{ ");
            while (node != null) {
                stringBuilder.append("["+ node.toString() +"],");
                node = node.next;
            }
        }
        String result = stringBuilder.toString();
        int index = result.lastIndexOf(",");
        return result.substring(0, index) + " }";
    }

    /**
     * 判断某个元素是否在链表中
     * @param element
     * @return
     */
    public boolean contains(E element) {
        return getIndexOf(element) != -1;
    }

    /**
     * 获取一个元素
     * @param index
     * @return
     */
    public E get(int index) {
        return (E) getNode(index).element;
    }

    /**
     * 获取最后一个元素
     * @return
     */
    public E getLast() {
        return (E) last.element;
    }

    /**
     * 获取第一个元素
     * @return
     */
    public E getFirst() {
        return (E) first.element;
    }

    /**
     * 替换特定索引的元素
     * @param index
     * @param element
     */
    public void replace(int index, E element) {
        getNode(index).element = element;
    }

    /**
     * 获取某个元素的索引,这里通常是第一次出现的索引
     * @param element
     * @return
     */
    public int getIndexOf(E element) {
        int index = 0;
        if (element == null) {
            for (Node<E> node = first; node != null; node = node.next) {
                if (node.element == null) {
                    return index;
                }
                index++;
            }
        }
        else {
            for (Node<E> node = first; node != null; node = node.next) {
                if (node.element.equals(element)) {
                    return index;
                }
                index++;
            }
        }
        return -1;
    }

    /**
     * 获取某个元素最后一次出现的索引
     * @param element
     * @return
     */
    public int getLastIndexOf(E element) {
        int index = size();
        if (element == null) {
            for (Node<E> node = last; node != null; node = node.prev) {
                index--;
                if (node.element == null) {
                    return index;
                }
            }
        }
        else {
            for (Node<E> node = last; node != null; node = node.prev) {
                index--;
                if (node.element.equals(element)) {
                    return index;
                }
            }
        }
        return -1;
    }

    /**
     * 移除某个元素第一次出现的位置
     * @param element
     */
    public void removeFirstOccurrence(E element) {
        if (element == null) {
            return;
        }
        int index = getIndexOf(element);
        remove(index);

    }

    /**
     * 移除某个元素最后一次出现的位置
     * @param element
     */
    public void removeLastOccurrence(E element) {
        if (element == null) {
            return;
        }
        int index = getLastIndexOf(element);
        remove(index);
    }
}
```

测试代码:

``` java
public class TestDoubleLinkedList {

    public static void main(String[] args) {

        DoubleLinkedList<User> list = new DoubleLinkedList<User>();
        list.add(new User(15, "Amy", "Girl"));
        list.add(new User(14, "Tony", "Boy"));
        list.add(new User(17, "Masha", "Girl"));
        list.addLast(new User(20, "Shabby", "Boy"));
        list.addFirst(new User(11, "Sasa", "Girl"));
        list.add(new User(14, "Tony", "Boy"));
        //System.out.println(list.contains(new User(14, "Tony", "Boy")));
        //list.removeFirst();
        //list.removeLast();
        //list.removeLastOccurrence(new User(14, "Tony", "Boy"));
        //System.out.println(list.getLastIndexOf(new User(14, "Tony", "Boy")));
        System.out.println(list.get(4));
        System.out.println(list.toString());
    }
}
```

以上测试代码，所有功能均通过测试。完成！