---
title: 关于List-contains方法的一些思考
date: 2016-04-09 21:34:14
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-2-thumbnail.png
tags:
	- JDK8
	- List
	- contains方法
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
	- List
	- contains方法
---
实现了一个单链表，尤其是写contains方法的时候陷入了一个瓶颈，该方法对于String， Integer， Float， Double等等数据类型有效，对于**对象**却无效，我在想是我的实现的代码有问题吗？

代码如下：

``` java
/**
 * 判断某个元素是否在链表中
 * @param element
 * @return 若在即为true，不在即为false
 */
public boolean contains(E element) {
    return (getIndexOf(element) != -1);
}

/**
 * 获取某个元素的索引, 参考双向链接表的实现
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
```

发现这里面是没有问题的，我采用的是类似与LinkedList的实现方式，然后再来测试对象的，发现持续报错！于是我又继续测试Java自带的一些List的实现类，试了两个，ArrayList和LinkedList，然后结果就是contains一直显示**false**！！！

查看了equals最初的源码，也就是所有类的祖先Object类，发现了这个：

``` java
public boolean equals(Object obj) {
    return (this == obj);
}
```

原来String以及其它几个基本类型都自己实现了**equals**方法，于是我就想，User类和String都是属于类，他们的实例不是基本类型，所以他们必须得自己单独实现自己的equals方法。
然后下面就是User类：

``` java
public class User{

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

    /**
     * 重写 equals方法
     * @param object
     * @return
     */
    @Override
    public boolean equals(Object object) {
        if (object instanceof User) {
            User otherUser = (User) object;
            if (this.age == otherUser.getAge()
                    && this.gender.equals(otherUser.getGender())
                    && this.name.equals(otherUser.getName())) {
                return true;
            }
        }
        return false;
    }
}
```

从最后这个代码可以看出，我们是重载了equals方法，**注意重载的时候参数类型一定要相同，否则绝对报错！如果类型以及个数不一样，那就是重写了**。这点得非常小心！
然后再测试，一切OK！