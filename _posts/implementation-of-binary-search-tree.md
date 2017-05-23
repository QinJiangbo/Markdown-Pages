---
title: BinarySearchTree查找二叉树独立实现
date: 2016-07-12 23:15:45
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/btree-thumbnail.png
tags:
	- 数据结构
	- 二叉树
categories:
	- 架构师
	- 算法
keywords:
	- 数据结构
	- 二叉树
---

### 先看看实现了哪些功能吧？

（1）构造二叉树

（2）遍历二叉树结点

（3）搜索二叉树结点

（4）删除二叉树结点

（5）判断结点是否存在二叉树

### 上代码

``` java
package hk.inso.service;

/**
 * Created by IntelliJ IDEA.
 * Date: 8/17/15 11:45 PM
 * Author: Richard
 */
public class BinarySearchTree {

    /**
     * 根结点，是所有遍历的入口
     */
    private Node root = null;
    private StringBuilder stringBuilder = new StringBuilder();

    /**
     * 插入一个元素
     * @param value
     */
    public void add(int value) {
        Node node = new Node();
        node.setValue(value);
        insert(node);
    }

    /**
     * 显示root
     * @return
     */
    public String getRoot() {
        return "Root{ " + root.value + " }";
    }

    /**
     * 移除一个特定的元素
     * @param value
     */
    public void remove(int value) {
        Node node = new Node();
        node.setValue(value);
        delete(node);
    }

    /**
     * 获取某个元素
     * @param value
     * @return
     */
    public String get(int value) {
        Node node = new Node();
        node.setValue(value);
        Node resultNode = search(node);
        return "parent: " + resultNode.parent + " left: " +
                resultNode.left + " right: " + resultNode.right;
    }

    /**
     * 判断是否包含某个元素
     * @param value
     * @return
     */
    public boolean contains(int value) {
        Node node = new Node();
        node.setValue(value);
        Node resutlNode = search(node);
        if (resutlNode == null) {
            return false;
        }
        return true;
    }

    /**
     * 先序遍历树，输出全部元素
     * @return
     */
    @Override
    public String toString(){
        //很重要，flush StringBuilder对象
        stringBuilder = new StringBuilder();
        StringBuilder sb = new StringBuilder();
        sb.append("BSTree{ ");
        String midString = midTraverse(root);
        //String midString = preTraverse(root);
        //String midString = postTraverse(root);
        int index = midString.lastIndexOf(", ");
        midString = midString.substring(0, index);
        sb.append(midString);
        sb.append(" }");
        return sb.toString();
    }

    /**
     * 中序遍历树结构
     * @param node
     * @return
     */
    private String midTraverse(Node node) {

        if (node == null) {
            return null;
        }
        midTraverse(node.left);
        stringBuilder.append(node.value + ", ");
        midTraverse(node.right);
        return stringBuilder.toString();
    }

    /**
     * 先序遍历树结构
     * @param node
     * @return
     */
    private String preTraverse(Node node) {
        if (node == null) {
            return null;
        }
        stringBuilder.append(node.value + ", ");
        preTraverse(node.left);
        preTraverse(node.right);
        return  stringBuilder.toString();
    }

    /**
     * 后序遍历树结构
     * @param node
     * @return
     */
    private String postTraverse(Node node) {
        if (node == null) {
            return null;
        }
        postTraverse(node.left);
        postTraverse(node.right);
        stringBuilder.append(node.value + ", ");
        return stringBuilder.toString();
    }

    /**
     * 插入结点到二叉查找树
     * @param node
     */
    private void insert(Node node){

        if (root == null) {
            root = node;
        }
        else {
            boolean flag = true;
            Node iteratorNode = root;
            while (flag) {
                //无子结点
                if (iteratorNode.left == null && iteratorNode.right == null) {
                    appendNode(node, iteratorNode);
                    flag = false;
                }
                //有子结点
                else {
                    //插入比当前结点小的结点
                    if (node.value < iteratorNode.value) {
                        if (iteratorNode.left == null) {
                            appendNode(node, iteratorNode);
                            flag = false;
                        } else {
                            iteratorNode = iteratorNode.left;
                        }
                    }
                    //插入比当前结点大的结点
                    else {
                        if (iteratorNode.right == null) {
                            appendNode(node, iteratorNode);
                            flag = false;
                        } else {
                            iteratorNode = iteratorNode.right;
                        }
                    }
                }
            }
        }
    };

    /**
     * 添加子结点
     * @param node
     * @param parent
     */
    private void appendNode(Node node, Node parent) {

        if (node.value < parent.value) {
            parent.left = node;
            node.parent = parent;
        }
        else {
            parent.right = node;
            node.parent = parent;
        }
    }

    /**
     * 删除结点
     * @param node
     */
    private void delete(Node node){
        //找到该结点
        Node resultNode = search(node);

        if (resultNode.value == root.value) {
            if (resultNode.left != null && resultNode.right != null) {
                Node maxNode = getMaxNode(resultNode.left);
                maxNode.right = resultNode.right;
                resultNode.right.parent = maxNode;
                root = resultNode.left;
            } else if (resultNode.left == null && resultNode.right != null) {
                root = resultNode.right;
            } else if (resultNode.right == null && resultNode.left != null) {
                root = resultNode.left;
            } else if (resultNode.right == null && resultNode.left == null) {
                root = null;
            }
        }

        else if (resultNode.left == null && resultNode.right == null) {
            if (resultNode.isLeft()) {
                resultNode.parent.left = null;
            }
            else {
                resultNode.parent.right = null;
            }
        }

        else if (resultNode.left != null && resultNode.right == null) {
            if (resultNode.isLeft()) {
                resultNode.parent.left = resultNode.left;
            }
            else {
                resultNode.parent.right = resultNode.left;
            }
            resultNode.left.parent = resultNode.parent;
        }

        else if (resultNode.left == null && resultNode.right != null) {
            if (resultNode.isLeft()) {
                resultNode.parent.left = resultNode.right;
            }
            else {
                resultNode.parent.right = resultNode.right;
            }
            resultNode.right.parent = resultNode.parent;
        }

        /**
         * 将当前结点的右孩子连接到左子树的最大子结点，并作为这个最大子结点的右孩子
         */
        else if (resultNode.left != null && resultNode.right != null) {
            if (resultNode.isLeft()) {
                resultNode.left.parent = resultNode.parent;
                resultNode.parent.left = resultNode.left;
                Node maxNode = getMaxNode(resultNode.left);
                maxNode.right = resultNode.right;
                resultNode.right.parent = maxNode;
            } else {
                resultNode.left.parent = resultNode.parent;
                resultNode.parent.right = resultNode.left;
                Node maxNode = getMaxNode(resultNode.left);
                maxNode.right = resultNode.right;
                resultNode.right.parent = maxNode;

            }
        }
    };

    /**
     * 查找某个元素
     * @param node
     * @return
     */
    private Node search(Node node){
        Node iteratorNode = root;
        if (node == null || iteratorNode == null) {
            return null;
        }
        boolean flag = true;
        while (flag) {

            if (node != null && iteratorNode != null && node.value < iteratorNode.value) {
                if (iteratorNode.isLeaf()){
                    node = null;
                    flag = false;
                }
                else {
                    iteratorNode = iteratorNode.left;
                }

            }
            if (node != null && iteratorNode != null && node.value > iteratorNode.value) {
                if (iteratorNode.isLeaf()){
                    node = null;
                    flag = false;
                }
                else {
                    iteratorNode = iteratorNode.right;
                }

            }
            //一定要分析清楚这个地方
            if (node != null && iteratorNode != null && node.value == iteratorNode.value) {
                node = iteratorNode;
                flag = false;
            }

            if (iteratorNode == null || node == null) {
                node = null;
                flag = false;
            }
        }
        return node;
    };

    /**
     * 获取当前结点作为根的树下最小值的结点
     * @param node
     * @return
     */
    private Node getMinNode(Node node) {
        Node iteratorNode = node;
        while (iteratorNode.left != null) {
            iteratorNode = iteratorNode.left;
        }
        return iteratorNode;
    }

    /**
     * 获取当前结点作为根的树下最大值的结点
     * @param node
     * @return
     */
    private Node getMaxNode(Node node) {
        Node iteratorNode = node;
        while (iteratorNode.right != null) {
            iteratorNode = iteratorNode.right;
        }
        return iteratorNode;
    }

    /**
     * 内部类，树结点
     */
    class Node{

        private Node parent = null;
        private Node left = null;
        private Node right = null;
        private int value = 0;

        public Node(Node parent, Node left, Node right, int value) {
            this.parent = parent;
            this.left = left;
            this.right = right;
            this.value = value;
        }

        public Node() {
            //constructor without args
        }

        public Node getParent() {
            return parent;
        }

        public void setParent(Node parent) {
            this.parent = parent;
        }

        public Node getLeft() {
            return left;
        }

        public void setLeft(Node left) {
            this.left = left;
        }

        public Node getRight() {
            return right;
        }

        public void setRight(Node right) {
            this.right = right;
        }

        public int getValue() {
            return value;
        }

        public void setValue(int value) {
            this.value = value;
        }

        /**
         * 是否为左孩子
         * @return
         */
        public boolean isLeft() {
            if (this.parent.left != null && this.value == this.parent.left.value) {
                return true;
            }
            return false;
        }

        /**
         * 是否为叶子结点
         * @return
         */
        public boolean isLeaf() {
            if (this.left == null && this.right == null) {
                return true;
            }
            return false;
        }

        @Override
        public String toString() {
            return "Node{" +
                    "value=" + value +
                    "}";
        }
    }
}
```

测试代码：

``` java
public class TestBinarySearchTree {

    private static BinarySearchTree binarySearchTree = new BinarySearchTree();

    public static void main(String[] args) {

        binarySearchTree.add(14);
        binarySearchTree.add(20);
        binarySearchTree.add(7);
        binarySearchTree.add(11);
        binarySearchTree.add(19);
        binarySearchTree.add(2);
        binarySearchTree.add(44);
        binarySearchTree.add(32);
        binarySearchTree.add(4);

        binarySearchTree.remove(32);
        System.out.println(binarySearchTree.toString());
        System.out.println(binarySearchTree.contains(32));
    }
}
```

查看结果:

``` bash
BSTree{ 2, 4, 7, 11, 14, 19, 20, 44 }
false
```

以上源码所对应的结点的值均为Int型，如果你感兴趣，可以更改源码，扩展它的类型。以上代码均通过测试。