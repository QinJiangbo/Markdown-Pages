---
title: 中文和Unicode互相转化
date: 2016-05-06 23:42:59
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/cn-unicode-thumbnail.png
tags:
	- Unicode
	- 中文
categories:
	- 开发技术
	- Java
keywords:
	- Unicode
	- 中文
---

Unicode转中文

```java
String unicode = "\u6211\u7231\u7956\u56fd";
String result = new String(unicode.getBytes("UTF-8"), "UTF-8");
System.out.println(result);
```

结果：我爱祖国

中文转Unicode

``` java
String chinese = "我爱祖国";
StringBuffer unicode = new StringBuffer();
for (int i = 0; i < chinese.length(); i++) {
    Character character = chinese.charAt(i);
    unicode.append("\\u" + Integer.toHexString(character));
}
System.out.println(unicode.toString());
```

结果：\u6211\u7231\u7956\u56fd