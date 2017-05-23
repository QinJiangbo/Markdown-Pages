---
title: URL缩短原理与实现
date: 2017-03-08 01:23:19
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/URL-Shortener.png
tags:
	- URL缩短
categories:
	- 架构师
	- 算法
keywords:
	- URL缩短
---
![](https://obrxbqjbi.qnssl.com/blog/image/URL-Shortener.png)

一直在使用URL缩短的技术，但是一直没有时间来琢磨这背后的技术原理是什么，今天给大伙儿聊聊URL缩短技术的原理和实现细节。

## 短网址是什么？
还记得**http://t.cn/RTwgi8**这样的网址么？没错，我们每次打开新浪微博的时候只要是链接，基本上都是这样一种形式。简单点来说，短网址就是长度比较短的网址。从另一种角度上来讲，短网址的确产生了巨大的价值。以微博为例，微博一般限制字数为140字，如果你粘贴一个很长的链接要分享给大家，结果就占据了你微博的一半，这肯定是不能接受的。所以，微博就推出了短网址的服务，大家在发送微博时，不管链接是视频还是文章等等，统统转化为类似**http://t.cn/**这样的短网址。

## 短网址服务提供商
- [百度短网址](http://dwz.cn)
- [新浪短网址](http://sina.lt/)
- [缩我](http://suo.im)
- [谷歌短网址](https://goo.gl)
- [YoURLs](http://yourls.org/)
- [TinyURL](http://tinyurl.com/)

## 原理
一般来说有两种实现原理，一种是基于MD5加密的，另一种是根据[a-zA-Z0-9]组成的62个字母作为字典表来转换的。

### MD5加密
先说说MD5加密算法实现的短网址服务，主要分为以下几步：

1. 拿到长网址以后对长网址生成32位的md5签名串， 分为4个段，每一个段有8个字符。
2. 然后将4个小段分别和**0x3fffffff**(7个f)作**与操作**，超过30位以上的部分直接截断。
3. 将这30位分为六个段，每个5位，根据这5位的数字取得对应在字母表中的字符。总共是6个字符。
4. 然后就可以获得4个6位的字符串，随便选择一个作为短网址即可。
5. 将短网址和长网址一起对应地存入到数据库中，方便到时候直接从RDBMS或者是NoSQL数据库中直接读取。

这种方法比较简单，但是存在一个问题就是还是存在一定的可能性会重复。所以我不推荐大家使用这种方式去做，毕竟用户的数据安全是第一位的。

### [a-zA-Z0-9]字母表法
我时常在想，新浪哪能容纳得了那么多的链接啊？这么多短网址怎样才能够用呢？其实大家不用担心，你们自己可以算一下，根据这个字母表生成6位字符串总共有多少种可能性？
$$
62 ^ 6 = 56800235584
$$
没错，就是**56800235584**这么多种，约为568亿个链接。足够用啦！

好了，我们来说说它的具体操作流程。这里我们要约定一个数据字典，而且应该是一个双向的字典。比如`a->0`，`b->1`，`c->2`,...,`9->61`等等。分两部分解释编码和解码的步骤。

### 编码步骤（数据库记录ID-->短网址）
我们获取到一个长网址以后，会先将它存入到数据库，不管是RDBMS关系型数据库还是NoSQL数据库，存好以后会返回它的一个记录编号（一般是表的行号）。首先需要明确的是这个编号是一个10进制的数字，现在我们需要做的工作**就是将其转化为62进制**。为什么要这么做？因为我们的字典是62个字符组成的，所以想要与其一一对应的话必须按照62进制来算。进制转换想必大家应该还记得吧。

`注意`如果遇到记录编号为12， 45， 29等等比较小的ID时，可能凑不齐6位字符串。这个时候我们就需要在它们的前面加上`a`，因为`a->0`。举个例子，记录ID为60的数据对应的短网址就应该是`aaaaa8`，因为`8->60`，前面不够的直接补上`a`即可。

### 解码步骤（短网址--> 数据库记录ID）
解码步骤相对简单一点，就是将短网址的各个字符转化为对应的数字然后相加即可。还是举个栗子：**Rwis8d**这个短网址的对应的62进制数据为**[43, 22, 8, 18, 60, 3]**。可以按照如下方式计算：
$$
43 \times 62^5 + 22 \times 62^4  + 8 \times 62 ^ 3 + 18 \times 62 ^ 2 + 60 \times 62 ^ 1 + 3 \times 62 ^ 0  = 39720770707
$$
所以可以得到数据库中的编号为**39720770707**根据这个去查找数据库中对应的长网址就行啦！

## 代码实现
代码采用Java实现，有需要的大家可以根据这个Java版本写出自己擅长的语言的版本。欢迎大家尝试！

``` java
package URLShortener;

import java.util.HashMap;
import java.util.Map;

/**
 * @date: 08/03/2017 9:25 PM
 * @author: qinjiangbo@github.io
 */
public class URLShortener {

   private static final String DICT = "abcdefghijklmnopqrstuvwxyz" +
            "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    // 数字->字符映射
    private static final char[] CHARS = DICT.toCharArray();
    // 字符->数字映射
    private static final Map<Character, Integer> NUMBERS = new HashMap<>();

    static {
        int len = CHARS.length;
        for (int i = 0; i<len; i++) {
            NUMBERS.put(CHARS[i], i);
        }
    }

    /**
     * 根据从数据库中返回的记录ID生成对应的短网址编码
     * @param id (1-56.8billion)
     * @return
     */
    public static String encode(long id) {
        StringBuilder shortURL = new StringBuilder();
        while (id > 0) {
            int r = (int) (id % 62);
            shortURL.insert(0, CHARS[r]);
            id = id / 62;
        }
        int len = shortURL.length();
        while (len < 6) {
            shortURL.insert(0, CHARS[0]);
            len++;
        }
        return shortURL.toString();
    }

    /**
     * 根据获得的短网址编码解析出数据库中对应的记录ID
     * @param key 短网址 eg. RwTji8, GijT7Y等等
     * @return
     */
    public static long decode(String key) {
        char[] shorts = key.toCharArray();
        int len = shorts.length;
        long id = 0l;
        for (int i = 0; i < len; i++) {
            id = id + (long) (NUMBERS.get(shorts[i]) * Math.pow(62, len-i-1));
        }
        return id;
    }

    public static void main(String[] args) {
        System.out.println(encode(39729551080l));
        System.out.println(decode("RwTji8"));
    }
}

```

控制台输出结果：

``` bash
RwTji8
39729551080

Process finished with exit code 0
```

## 总结
短网址是一个比较实用的算法设计，它通过简单的算法就能够将无论多长的长网址转换为只有短短6位的短网址。这里有人会有疑问了，为什么是6位？我没有说一定是6位啊，哈哈，5位，4位，以及7位，8位都是可以的，具体取决于你的业务设计。位数越小，越容易记住，当然了，容纳的长网址数量越少。位数越大，越难记住，但是容量会指数倍的增加。根据这两个标准去判断是不错的啦！
