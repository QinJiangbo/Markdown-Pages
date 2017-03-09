---
title: Guava优美代码-18-哈希
date: 2016-11-06 23:40:11
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Hash
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Hash
---
## Guava哈希算法
Java内建的散列码(hash code)概念被限制为32位，并且没有分离散列算法和它们所作用的数据，因此很难用备选算法进行替换。此外，使用Java内建方法实现的散列码通常是劣质的，部分是因为它们最终都依赖于JDK类中已有的劣质散列码。

`Object.hashCode`往往很快，但是在预防碰撞上却很弱，也没有对分散性的预期。这使得它们很适合在散列表中运用，因为额外碰撞只会带来轻微的性能损失，同时差劲的分散性也可以容易地通过再散列来纠正（Java中所有合理的散列表都用了再散列方法）。然而，在简单散列表以外的散列运用中，`Object.hashCode`几乎总是达不到要求——因此，Guava为我们提供了补充的Hash工具类。

## Guava哈希工具类

### HashFunction
HashFunction是一个单纯的（引用透明的）、无状态的方法，它把任意的数据块映射到固定数目的位指，并且保证相同的输入一定产生相同的输出，不同的输入尽可能产生不同的输出。

### Hasher
HashFunction的实例可以提供有状态的Hasher，Hasher提供了流畅的语法把数据添加到散列运算，然后获取散列值。Hasher可以接受所有原生类型、字节数组、字节数组的片段、字符序列、特定字符集的字符序列等等，或者任何给定了Funnel实现的对象。

### Funnel
Funnel描述了如何把一个具体的对象类型分解为原生字段值，从而写入PrimitiveSink。

### HashCode
一旦Hasher被赋予了所有输入，就可以通过`hash()`方法获取HashCode实例（多次调用`hash()`方法的结果是不确定的）。HashCode可以通过`asInt()`、`asLong()`、`asBytes()`方法来做相等性检测，此外，`writeBytesTo(array, offset, maxLength)`把散列值的前maxLength字节写入字节数组。

### Hashing
Hashing类提供了若干哈希函数，以及运算HashCode对象的工具方法。

- md5
- sha256
- sha512
- sha1
- murmur3_128
- murmur3_32
- goodFastHash

## 哈希工具类使用实例

### 防碰撞哈希
``` java
@Test
public void testMd5Hash() {
    Student student1 = new Student(2012302580314l, 21, "Richard");
    Student student2 = new Student(2012302580311l, 21, "Richard");
    HashFunction hashFunction = Hashing.md5();
    HashCode hashCod1 = hashFunction.newHasher()
            .putObject(student1, (Funnel<Student>) (from, into) -> into
                    .putLong(from.sid)
                    .putInt(from.age)
                    .putString(from.name, Charsets.UTF_8)).hash();
    HashCode hashCode2 = hashFunction.newHasher()
            .putObject(student2, (Funnel<Student>) (from, into) -> into
                    .putLong(from.sid)
                    .putInt(from.age)
                    .putString(from.name, Charsets.UTF_8)).hash();
    System.out.println(hashCod1.asInt());
    System.out.println(hashCode2.asInt());
}

class Student {
    final long sid;
    final int age;
    final String name;

    Student(long sid, int age, String name) {
        this.sid = sid;
        this.age = age;
        this.name = name;
    }
}
```

### 一致性哈希

``` java
@Test
public void testConsistentHash() {
    List<String> ips = Lists.newArrayList("192.168.1.100",
            "192.168.1.110", "192.168.1.120");
    long ipHashCode1 = Hashing.md5().newHasher().putString(ips.get(0), Charsets.UTF_8).hash().asLong();
    long ipHashCode2 = Hashing.md5().newHasher().putString(ips.get(1), Charsets.UTF_8).hash().asLong();
    long ipHashCode3 = Hashing.md5().newHasher().putString(ips.get(2), Charsets.UTF_8).hash().asLong();

    System.out.println("ip1: " + Hashing.consistentHash(ipHashCode1, 3));
    System.out.println("ip2: " + Hashing.consistentHash(ipHashCode2, 3));
    System.out.println("ip3: " + Hashing.consistentHash(ipHashCode3, 3));
}
```

上面两个例子，分别从防碰撞和分布性两个角度来说明哈希工具类的使用方法的。首先是防碰撞，第一个测试采用了md5方式，当然了，还可以使用其他的一些算法，不过md5算法是比较好的选择，这种方式能有效地保证各个对象的hash值不发生碰撞。第二种方式是一致性哈希算法，这种方式适用范围较广，一般用在分布式环境下，当请求过多的时候，一致性哈希能有效地将请求均匀地发送到不同的主机上执行，保证了很好的负载均衡。

## 总结
关于一致性哈希我建议读者到CSDN的这篇博文下好好了解一下，这篇文章的作者对一致性哈希有非常透彻的理解，能帮助大家很好地理解一致性哈希是什么以及它能做什么。

CSDN博文地址：[http://blog.csdn.net/cywosp/article/details/23397179/](http://blog.csdn.net/cywosp/article/details/23397179/)