---
title: Submodual
date: 2016-06-14 09:39:05
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/submodual-thumbnail.jpg
tags:
	- 子模
	- Submodual
categories:
	- 数据科学
keywords:
	- 子模态
	- 社交网络
mathjax: true
---
## 子模态定义
子模态是集合函数的一种性质。一个集合函数$f(x)$的定义要满足下面这个性质
$$f:2^\Omega \to \mathbb R \tag {1}$$
即$f(x)$的定义域为集合$\Omega$的任何一个子集，值域为实数集。而这个集合函数如果要满足子模态性质的话，还需要满足下面三个等价条件中的任何一个。

- 对于任何一个$X,Y\subset\Omega$且$X\subset Y$，以及对任何一个$X, Y\in\Omega$，下面的式子一定成立。
$$f(X\cup x)−f(X)\geq f(Y\cup x)−f(Y)$$
- 对于任何一个$X,Y\subset\Omega$，下面的式子一定成立。
$$f(X)+f(Y)\geq f(X\cup Y)+f(X\cap Y)$$
- 对于任何一个$X\subset\Omega$及$x_1,x_2\in\Omega$，下面的式子一定成立
$$f(X\cup x_1)+f(X\cup x_2)\geq f(X\cup x_1,x_2)+f(X)$$

读者可以很轻松的验证这几个条件是等价的，这里就不去证明了。

根据这些条件，我们可以得出下面这个结论。设$f_1(x),f_2(x),⋯,f_k(x)$都是有子模态性质的函数，$c_1,c_2,⋯,c_k$是非负实数，则下面这个函数
$$F(x) = \sum_{i=1}^k c_if_i(x) \tag {2}$$
仍然是子模态的。即多个子模态函数的非负线性组合仍然是子模态函数。这个推论可以很简单的验证出来，这里也就懒得做证明的。

## 子模态与贪心方法
接下来的问题就是，子模态这个函数性质有什么用。简单来说子模态这个函数性质在组合优化中很有用。特别是针对保持$\mid x \mid$的大小固定，然后去求$f(x)$的最大值这种问题。 这个子模态性质可以保证我们在贪心求解这个问题得到的解不会差，事实上贪心解$f(x)$的值不会小于$(1−1/e)∗OPT$，其中$OPT$为最优解。在该问题的贪心求解过程中，我们是从$0$开始构建目标集合$x$的。每一步我们将一个新的元素加入到$x$中，迭代$k$步。我们以$X_i$来代表迭代$i$次之后的$x$集合，初始$x_0 = \phi$。我们对下一个要加入的节点$u$的选取的贪心规则为
$$u=argmax_u f(X_{i−1}\cup u) \tag {3}$$
现在我们要证明在此贪心规则下
$$f(x)\geq(1−\frac{1}{e})*OPT \tag {4}$$
为此，我们先证明一个引理
$$f(A\cup B)−f(A)\leq\sum_{j=1}^k[f(A\cup b_j)−f(A)] \tag {5}$$
其中$B=b_1,⋯,b_k$.此时我们定义$B_i=b_1,⋯,b_i$，则
$$
\begin {aligned} 
f(A\cup B)−f(A) &= \sum_{i=1}^k[f(A\cup B_i)−f(A\cup B_{i−1})] \\\
&=\sum_{i=1}^k[f(A\cup B_{i−1}\cup b_i)−f(A\cup B_{i−1})] \\\
&≤\sum_{j=1}^k[f(A\cup b_j)−f(A)]
\end {aligned} 
\tag {6} 
$$
设最优解为$T=t_1,⋯,t_k,\delta_i=f(X_i)−f(X_{i−1})$,根据上面这个不等式我们可以推出
$$
\begin {aligned}
f(T) &≤ f(X_i\cup T) \\\
&=f(X_i\cup T)−f(X_i)+f(X_i) \\\
&≤\sum_{j=1}^k[f(X_i\cup t_j)−f(X_i)]+f(X_i) \\\
&≤\sum_{j=1}^k[\delta_{i+1}]+f(X_i) \\\
&=f(X_i)+k\delta_{i+1}
\end {aligned}
\tag {7} 
$$
综上我们可以得出这个结论
$$\delta_{i+1}≥\frac{1}{k}[f(T)−f(X_i)] \tag {8}$$
所以
$$
\begin {aligned}
f(X_{i+1})&=f(X_i)+\delta_{i+1} \\\
&≥f(X_i)+\frac{1}{k}[f(T)−f(X_i)] \\\
&=(1−\frac{1}{k})f(X_i)+\frac{1}{k}f(T)
\end {aligned}
\tag {9} 
$$
通过上面的递推式，我们可以给出$f(X_i)$
$$f(X_i)≥[1−(1−\frac{1}{k})^i]f(T) \tag {10}$$
这个是简单的递推数列求通项，这里也就懒得证明了。所以
$$f(X_k)≥[1−(1−\frac{1}{k})^k]f(T)≥[1−\frac{1}{e}]f(T) \tag {11}$$
这就是贪心大法好的证明。

#### 本文参考链接: [http://spiritsaway.info/algorithm/submodual.html](http://spiritsaway.info/algorithm/submodual.html)
