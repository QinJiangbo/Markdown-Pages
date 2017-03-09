---
title: 'Fisrt Bite of Hexo'
date: 2016-08-12 01:24:51
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/hexo-thumbnail.png
tags: 
	- 生活 
	- 技术
categories: 
	- 经验感悟
keywords: [Hexo,Github]
mathjax: true
---

# Markdown语法简要说明

## 标题
在 Markdown 中，你只需要在文本前面加上 # 即可，同理、你还可以增加二级标题、三级标题、四级标题、五级标题和六级标题，总共六级，只需要增加  # 即可，标题字号相应降低。

	# 一号标题
	## 二号标题
	### 三号标题
	#### 四号标题
	##### 五号标题
	###### 六号标题

## 列表
在 Markdown 中，你只需要在文字前面加上 - 就可以了

	- 文本一
	- 文本二
	- 文本三

也可以加上 * 或者 + 来表示.

	* 文本一
	* 文本二
	* 文本三

效果如下:

* 文本一
* 文本二
* 文本三

## 链接和图片
在 Markdown 中，插入链接不需要其他按钮，你只需要使用`[显示文本](链接地址)`这样的语法即可

	[简书](http://jianshu.io)
	
效果如下:
[简书](http://jianshu.io)

在 Markdown 中，插入图片不需要其他按钮，你只需要使用`![](图片链接地址)`这样的语法即可

	![](https://obrxbqjbi.qnssl.com/blog/image/salt-lake.jpg)
	
效果如下:
![](https://obrxbqjbi.qnssl.com/blog/image/salt-lake.jpg)

## 引用
在 Markdown 中，你只需要在你希望引用的文字前面加上 > 就好了
	
	> 一盏灯， 一片昏黄； 一简书， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。

效果如下:
> 一盏灯， 一片昏黄； 一简书， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。

## 粗体和斜体
Markdown 的粗体和斜体也非常简单，用两个 \* 包含一段文本就是粗体的语法，用一个 \* 包含一段文本就是斜体的语法。
	
	*一盏灯*， 一片昏黄；**一简书**， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。
	
效果如下:

*一盏灯*， 一片昏黄；**一简书**， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。

## 表格
Markdown中的表格相对而言麻烦一点，但是还是非常简洁的。
	
	| Tables        | Are           | Cool  |
	| ------------- |:-------------:| -----:|
	| col 3 is      | right-aligned | $1600 |
	| col 2 is      | centered      |   $12 |
	| zebra stripes | are neat      |    $1 |
	
效果如下:

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | 1600 |
| col 2 is      | centered      |   12 |
| zebra stripes | are neat      |    1 |

## 显示链接中带括号的图片
	
	![][1]
	[1]: http://latex.codecogs.com/gif.latex?\prod%20\(n_{i}\)+1

效果如下:

![][1]
[1]: http://latex.codecogs.com/gif.latex?\prod%20\(n_{i}\)+1

## 插入代码
如果你是一名程序员，可以使用如下方法插入代码块.
	
	` ` ` java
	public void sayHello() {
		System.out.println("Hello");
	}
	` ` `
	
效果如下:

``` java
public void sayHello() {
	System.out.println("Hello");
}
```

## 插入数学公式
对于需要写公式的数学生来说，Markdown是不二选择！
	
	$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$

效果如下:

$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$


