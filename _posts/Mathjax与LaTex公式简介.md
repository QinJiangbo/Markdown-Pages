---
title: Mathjax与LaTex公式简介
date: 2016-08-14 22:40:59
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/latex-thumbnail.jpg
tags:
	- LaTEX
	- Hexo-Math
categories:
	- 经验感悟
keywords:
	- MathJax
	- Hexo博客
mathjax: true
---
## MathJax简介
[MathJax](http://www.mathjax.org/)是一款运行在浏览器中的开源的数学符号渲染引擎，使用MathJax可以方便的在浏览器中显示数学公式，不需要使用图片。目前，MathJax可以解析Latex、MathML和ASCIIMathML的标记语言。 MathJax项目于2009年开始，发起人有American Mathematical Society, Design Science等，还有众多的支持者，个人感觉MathJax会成为今后数学符号渲染引擎中的主流，也许现在已经是了。 本文接下来会讲述MathJax的基础用法，但不涉及MathJax的安装及配置。此外，推荐使用[StackEdit](https://stackedit.io/)学习MathJax的语法，它支持Markdown和MathJax，本文使用此编辑器撰写。

## 基础

### 公式标记与查看公式
使用MathJax时，需要用一些适当的标记告诉MathJax某段文本是公式代码。此外，MathJax中的公式排版有两种方式，inline和displayed。inline表示公式嵌入到文本段中，displayed表示公式独自成为一个段落。例如，$f(x)=3\times x$这是一个inline公式，而下面
$$f(x)=3\times x$$
是一个displayed公式。

在MathJax中，默认的displayed公式分隔符有`$$...$$` 和`\[…\]`,而默认的inline公式分隔符为 `(…)` ,当然这些都是可以自定义的，具体配置请参考文档。下文中，使用`$$…$$`作为displayed分隔符，`$…$`作为inline分隔符。

此外，可以在渲染完成的公式上方右键点击，唤出右键菜单。在菜单中提供了查看公式代码、设置显示效果和渲染模式的选项。


### 希腊字母
请参见下表：

|名称|大写|Tex|小写|Tex|
|----------|----------|----------|----------|----------|
|alpha	|$A$		|A		|$\alpha$		|\alpha
|beta	|$B$		|B		|$\beta$		|\beta
|gamma	|$\Gamma$		|\Gamma|$\gamma$		|\gamma
|delta	|$\Delta$		|\Delta	|$\delta$		|\delta
|epsilon	|$E$		|E		|$\epsilon$		|\epsilon
|zeta	|$Z$		|Z		|$\zeta$		|\zeta
|eta	       	|$H$		|H		|$\eta$		|\eta
|theta	|$\Theta$		|\Theta	|$\theta$		|\theta
|iota	       	|$I$		|I     		|$\iota$		|\iota
|kappa	|$K$		|K		|$\kappa$		|\kappa
|lambda	|$\Lambda$		|\Lambda|$\lambda$		|\lambda
|mu	       	|$M$ 		|M		|$\mu$		|\mu
|nu		|$N$		|N		|$\nu$		|\nu
|xi		|$\Xi$		|\Xi		|$\xi$		|\xi
|omicron	|$O$		|O		|$\omicron$		|\omicron
|pi		|$\Pi$		|\Pi		|$\pi$		|\pi
|rho		|$P$		|P		|$\rho$		|\rho
|sigma	|$\Sigma$		|\Sigma	|$\sigma$		|\sigma
|tau		|$T$		|T		|$\tau$		|\tau
|upsilon	|$\Upsilon$		|\Upsilon	|$\upsilon$		|\upsilon
|phi		|$\Phi$		|\Phi		|$\phi$		|\phi
|chi		|$X$		|X		|$\chi$		|\chi
|psi		|$\Psi$		|\Psi		|ψ		|\psi
|omega	|$\Omega$		|\Omega	|$\omega$		|\omega

### 上标与下标

上标和下标分别使用\^与\_，例如`x_i^2`：$x_i^2$。默认情况下，上下标符号仅仅对下一个组起作用。一个组即单个字符或者使用`{..}`包裹起来的内容。也就是说，如果使用10^10，会得到$10^10$，而10^{10}才是$10^{10}$。同时，大括号还能消除二义性，如x^5^6将得到一个错误，必须使用大括号来界定^的结合性，如`{x^5}^6`：${x^5}^6$ 或者 `x^{5^6}`：$x^{5^6}$。

### 括号

1. 小括号与方括号：使用原始的( )，[ ]即可，如`(2+3)[4+4]`：(2+3)[4+4]
2. 大括号：时由于大括号{}被用来分组，因此需要使用\\{和\\}表示大括号，也可以使用\\lbrace 和\\rbrace来表示。如`\{a*b\}:a∗b，\lbrace a*b \rbrace`：$\{a∗b\}, \lbrace a*b \rbrace$。
3. 尖括号：使用`\langle` 和 `\rangle`表示左尖括号和右尖括号。如`\langle x \rangle`：$\langle x \rangle$。
4. 上取整：使用`\lceil` 和 `\rceil` 表示。 如，`\lceil x \rceil`：$\lceil x \rceil$。
5. 下取整：使用`\lfloor` 和 `\rfloor` 表示。如，`\lfloor x \rfloor`：$\lfloor x \rfloor$。
6. 不可见括号：使用.表示。

需要注意的是，原始符号并不会随着公式大小缩放。如，`(\frac12)`：$\frac 1 2)$。可以使用`\left(…\right)`来自适应的调整括号大小。如下，

$$\{\sum_{i=0}^ni^2=\frac {(n^2+n)(2n+1)}{6}\} \tag {1.1}$$
$$\left\{\sum_{i=0}^ni^2=\frac {(n^2+n)(2n+1)}{6}\right\} \tag {1.2}$$

可以看到，公式1.2中的括号是经过缩放的。

### 求和与积分

- `\sum`用来表示求和符号，其下标表示求和下限，上标表示上限。如，`\sum_1^n`：$\sum_1^n$。

- `\int`用来表示积分符号，同样地，其上下标表示积分的上下限。如，`\int_1^\infty`：$\int_1^\infty$。

- 与此类似的符号还有，`\prod`：$\prod$，`\bigcup`:$\bigcup$，`\bigcap`：$\bigcap$，`\iint`：$\iint$。

### 分式与根式
分式的表示。第一种，使用`\frac a b`，`\frac`作用于其后的两个组a，b，结果为$\frac a b$。如果你的分子或分母不是单个字符，请使用`{..}`来分组。第二种，使用`\over`来分隔一个组的前后两部分，如`{a+1\over b+1}`：${a+1\over b+1}$。

根式使用`\sqrt`来表示。如，`\sqrt[4]{\frac xy}` ：$\sqrt[4]{\frac {x}{y}}$

### 字体

1. 使用`\mathbb`或`\Bbb`显示黑板粗体字，此字体经常用来表示代表实数、整数、有理数、复数的大写字母。如，$\mathbb{CHNQRZ}$。
2. 使用`\mathbf`显示黑体字，如，$\mathbf{ABCDEFGHIJKLMNOPQRSTUVWXYZ，abcdefghijklmnopqrstuvwxyz}$。
3. 使用`\mathtt`显示打印机字体，如，$\mathtt{𝙰𝙱𝙲𝙳𝙴𝙵𝙶𝙷𝙸𝙹𝙺𝙻𝙼𝙽𝙾𝙿𝚀𝚁𝚂𝚃𝚄𝚅𝚆𝚇𝚈𝚉，𝚊𝚋𝚌𝚍𝚎𝚏𝚐𝚑𝚒𝚓𝚔𝚕𝚖𝚗𝚘𝚙𝚚𝚛𝚜𝚝𝚞𝚟𝚠𝚡𝚢𝚣}$。
4. 使用`\mathrm`显示罗马字体，如，$\mathrm{ABCDEFGHIJKLMNOPQRSTUVWXYZ，abcdefghijklmnopqrstuvwxyz}$。
5. 使用`\mathscr`显示手写体，如，$\mathscr{ABCDEFGHIJKLMNOPQRSTUVWXYZ}$。
6. 使用`\mathfrak`显示Fraktur字母（一种德国字体），如$\mathfrak{ABCDEFGHIJKLMNOPQRSTUVWXYZ，abcdefghijklmnopqrstuvwxyz}$。

### 特殊函数与符号
1. 常见的三角函数，求极限符号可直接使用+缩写即可。如$sin x$, $arctan x$, $lim_{1\to\infty}$。
2. 比较运算符：`\lt \gt \le \ge \neq` ： $\lt \gt \le \ge \neq$。可以在这些运算符前面加上`\not`，如`\not\lt`：$\not\lt$。
3. `\times \div \pm \mp`表示：$\times \div \pm \mp$，`\cdot`表示居中的点，`x \cdot y` : $x \cdot y$。
4. 集合关系与运算：`\cup \cap \setminus \subset \subseteq \subsetneq \supset \in \notin \emptyset \varnothing` ：$\cup \cap \setminus \subset \subseteq \subsetneq \supset \in \notin \emptyset \varnothing$.
5. 表示排列使用`{n+1 \choose 2k}` 或 `\binom{n+1}{2k}`，${n+1 \choose 2k}$。
6. 箭头：`\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto` : $\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto$。
7. 逻辑运算符：`\land \lor \lnot \forall \exists \top \bot \vdash \vDash` ： $\land \lor \lnot \forall \exists \top \bot \vdash \vDash$。
8. `\star \ast \oplus \circ \bullet` ： $\star \ast \oplus \circ \bullet$。
9. `\approx \sim \cong \equiv \prec` ： $\approx \sim \cong \equiv \prec$。
10. `\infty \aleph_0` $\infty \aleph_0$ `\nabla \partial` $\nabla \partial$ `\Im \Re` $\Im \Re$。
11. 模运算 `\pmode`, 如，`a\equiv b\pmod n`：$a\equiv b\pmod n$。
12. `\ldots与\cdots`，其区别是dots的位置不同，ldots位置稍低，cdots位置居中。$a_1+a_2+\cdots+a_n，a_1,a_2,\ldots,a_n$。
13. 一些希腊字母具有变体形式，如 `\epsilon \varepsilon` : $\epsilon \varepsilon$, `\phi \varphi` $\phi \varphi$。

### 空间
通常MathJax通过内部策略自己管理公式内部的空间，因此a…b与a…….b（.表示空格）都会显示为ab。可以通过在ab间加入`\,`增加些许间隙，`\;`增加较宽的间隙，`\quad` 与 `\qquad` 会增加更大的间隙，如，$a\qquad b$。

### 顶部符号
对于单字符，`\hat x`：$\hat x$，多字符可以使用`\widehat {xy}`,$\widehat {xy}$.类似的还有`\hat,\overline,\vec,\overrightarrow, \dot \ddot` : $\hat {x} \quad \widehat {xy}\quad \overline {xyz} \quad \vec {abc} \quad \overrightarrow {xxx} \quad \dot{h} \quad \ddot{x}$。

### 结束
基础部分就是这些。需要注意的是一些MathJax使用的特殊字符，可以使用\转义为原来的含义。如`\$`表示`‘$‘`，`\_`表示下划线。

### 表格

使用`$$\begin{array}{列样式}…\end{array}$$`这样的形式来创建表格，列样式可以是clr表示居中，左，右对齐，还可以使用|表示一条竖线。表格中 各行使用\\分隔，各列使用&分隔。使用\hline在本行前加入一条直线。 例如

	$$
	\begin{array}{c|lcr}
	n & \text{Left} & \text{Center} & \text{Right} \\
	\hline
	1 & 0.24 & 1 & 125 \\
	2 & -1 & 189 & -8 \\
	3 & -20 & 2000 & 1+10i \\
	\end{array}
	$$

$$
\begin{array}{c|lcr}
n & \text{Left} & \text{Center} & \text{Right} \\
\hline
1 & 0.24 & 1 & 124 \\
2 & -11 & 189 & 90 \\
3 & -20 & 789 & 1+10i \\
\end{array}
$$

### 矩阵
### 基本用法
使用`$$\begin{matrix}…\end{matrix}$$`这样的形式来表示矩阵，在`\begin`与`\end`之间加入矩阵中的元素即可。矩阵的行之间使用`\\`分隔，列之间使用&分隔。

例如

	$$
	        \begin{matrix}
	        1 & x & x^2 \\
	        1 & y & y^2 \\
	        1 & z & z^2 \\
	        \end{matrix}
	$$
	
结果：
$$
\begin{matrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{matrix}
$$

### 加括号
如果要对矩阵加括号，可以像上文中提到的一样，使用`\left`与`\right`配合表示括号符号。也可以使用特殊的matrix。即替换`\begin{matrix}…\end{matrix}`中的matrix为pmatrix，bmatrix，Bmatrix，vmatrix,Vmatrix.
$$
\begin{pmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{pmatrix}
\tag {pmatrix}
$$

$$
\begin{bmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{bmatrix}
\tag {bmatrix}
$$

$$
\begin{Bmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{Bmatrix}
\tag {Bmatrix}
$$

$$
\begin{vmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{vmatrix}
\tag {vmatrix}
$$

$$
\begin{Vmatrix}
1 & x & x^2 \\
1 & y & y^2 \\
1 & z & z^2 \\
\end{Vmatrix}
\tag {Vmatrix}
$$

### 省略元素

可以使用`\cdots` $\cdots$ `\ddots` $\ddots$ `\vdots` $\vdots$来省略矩阵中的元素，如：

$$
\begin{pmatrix}
1 & a_1 & a_1^2 & a_1^3 & \cdots & a_1^n \\
2 & a_2 & a_2^2 & a_2^3 & \cdots & a_2^n \\
3 & a_3 & a_3^2 & a_3^3 & \cdots & a_3^n \\
4 & \vdots & \vdots & \vdots & \ddots & \vdots \\
4 & a_4 & a_4^2 & a_4^3 & \cdots & a_4^n \\
\end{pmatrix}
\tag {demo}
$$

### 增广矩阵
增广矩阵需要使用前面的array来实现，如
	
	$$ \left[
	      \begin{array}{cc|c}
	        1&2&3\\
	        4&5&6
	      \end{array}
	    \right]
	$$
结果：
$$
\left[
\begin{array}{cc|c}
1&2&3 \\
4&5&6 \\
\end{array}
\right]
$$

### 对齐的公式
有时候可能需要一系列的公式中等号对齐，如：

	$$
	\begin{align}
	\sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\
	 & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\ 
	 & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\
	 & = \frac{73}{12}\sqrt{1 - \frac{1}{73^2}} \\ 
	 & \approx \frac{73}{12}\left(1 - \frac{1}{2\cdot73^2}\right)
	\end{align}
	$$
	
$$
\begin{align}
\sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\\
 & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\\
 & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\\
 & = \frac{73}{12}\sqrt{1 - \frac{1}{73^2}} \\\ 
 & \approx \frac{73}{12}\left(1 - \frac{1}{2\cdot73^2}\right)
\end{align}
$$
这需要使用形如`\begin{align}…\end{align}`的格式，其中需要使用&来指示需要对齐的位置。请使用右键查看上述公式的代码。

### 分类表达式
定义函数的时候经常需要分情况给出表达式，可使用`\begin{cases}…\end{cases}`。其中，使用\来分类，使用&指示需要对齐的位置。如：

	$$
	f(n) =
	\begin{cases}
	n/2,  & \text{if $n$ is even} \\
	3n+1, & \text{if $n$ is odd}  \\
	\end{cases}
	$$
	
$$
f(n) =
\begin{cases}
n/2,  & \text{if n is even} \\\
3n+1, & \text{if n is odd}
\end{cases}
$$

上述公式的括号也可以移动到右侧，不过需要使用array来实现，如下：

	$$
	\left.
	\begin{array}{l}
	\text{if $n$ is even:}&n/2\\
	\text{if $n$ is odd:}&3n+1
	\end{array}
	\right\}
	=f(n)
	$$

$$
\left.
\begin{array}{l}
\text{if n is even:}&n/2\\
\text{if n is odd:}&3n+1
\end{array}
\right\}
=f(n)
$$

最后，如果想分类之间的垂直间隔变大，可以使用`\[2ex]`代替`\`来分隔不同的情况。(3ex,4ex也可以用，1ex相当于原始距离）。

### 数学符号查询
一般而言，从一个巨大的符号表中查询所需要的特定符号是一件令人沮丧的事情。在此向大家介绍一个$\TeX$手写符号识别系统，如下图：
![](https://obrxbqjbi.qnssl.com/blog/image/latex-format.png)
尽情享用吧~ [Detexify²](http://detexify.kirelabs.org/classify.html)。

### 空间问题
在使用Latex公式时，有一些不会影响公式正确性，但却会使其看上去很槽糕的问题。

### 不要在指数或者积分中使用 \frac
在指数或者积分表达式中使用`\frac`会使表达式看起来不清晰，因此在专业的数学排版中很少被使用。应该使用一个水平的/来代替，效果如下：
$$
\begin{array}{cc}
\mathrm{Bad} & \mathrm{Better} \\
\hline \\
e^{i\frac{\pi}2} \quad e^{\frac{i\pi}2}& e^{i\pi/2} \\
\int_{-\frac\pi2}^\frac\pi2 \sin x\,dx & \int_{-\pi/2}^{\pi/2}\sin x\,dx \\
\end{array}
$$

### 使用 \mid 代替 | 作为分隔符
符号|作为分隔符时有排版空间大小的问题，应该使用`\mid`代替。效果如下：
$$
\begin{array}{cc}
\mathrm{Bad} & \mathrm{Better} \\
\hline \\
\{x|x^2\in\Bbb Z\} & \{x\mid x^2\in\Bbb Z\} \\
\end{array}
$$

### 多重积分
对于多重积分，不要使用`\int\int`此类的表达，应该使用`\iint \iiint`等特殊形式。效果如下：
$$
\begin{array}{cc}
\mathrm{Bad} & \mathrm{Better} \\
\hline \\
\int\int_S f(x)\,dy\,dx & \iint_S f(x)\,dy\,dx \\
\int\int\int_V f(x)\,dz\,dy\,dx & \iiint_V f(x)\,dz\,dy\,dx
\end{array}
$$
此外，在微分前应该使用`\,`来增加些许空间，否则$\TeX$会将微分紧凑地排列在一起。如下：
$$
\begin{array}{cc}
\mathrm{Bad} & \mathrm{Better} \\
\hline \\
\iiint_V f(x)dz dy dx & \iiint_V f(x)\,dz\,dy\,dx
\end{array}
$$

### 连分数
书写连分数表达式时，请使用`\cfrac`代替`\frac`或者`\over`两者效果对比如下：
$$
x = a_0 + \cfrac{1^2}{a_1
+\, \cfrac{2^2}{a_2
+\, \cfrac{3^2}{a_3 + \cfrac{4^4}{a_4 + \cdots}}}} \tag{\cfrac}
$$

$$
x = a_0 + \frac{1^2}{a_1
+\, \frac{2^2}{a_2
+\, \frac{3^2}{a_3 + \frac{4^4}{a_4 + \cdots}}}} \tag{\frac}
$$

### 方程组
使用`\begin{array} … \end{array}`与`\left{…\right.`配合，表示方程组，如：
$$
\left\{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\\ 
a_2x+b_2y+c_2z=d_2 \\\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right.
$$

同时，还可以使用`\begin{cases}…\end{cases}`表达同样的方程组，如：
$$
\begin{cases}
a_1x+b_1y+c_1z=d_1 \\\
a_2x+b_2y+c_2z=d_2 \\\
a_3x+b_3y+c_3z=d_3
\end{cases}
$$

对齐方程组中的 = 号，可以使用 `\being{aligned} .. \end{aligned}`，如：
$$
\left\{
\begin{aligned} 
a_1x+b_1y+c_1z &=d_1+e_1 \\\ 
a_2x+b_2y&=d_2 \\\ 
a_3x+b_3y+c_3z &=d_3 
\end{aligned} 
\right.
$$

如果要对齐 = 号 和项，可以使用`\being{array}{列样式} .. \end{array}`，如：
$$
\left\{
\begin{array}{ll}
a_1x+b_1y+c_1z &=d_1+e_1 \\\ 
a_2x+b_2y &=d_2 \\\ 
a_3x+b_3y+c_3z &=d_3 
\end{array} 
\right.
$$

### 颜色
命名颜色是浏览器相关的，如果浏览器没有定义相关的颜色名称，则相关文本将被渲染为黑色。以下颜色是HTML4与CSS2标准中定义的一些颜色，其应该被大多数浏览器定义了。
$$
\begin{array}{|rc|}
\hline
\verb+\color{black}{text}+ & \color{black}{text} \\
\verb+\color{gray}{text}+ & \color{gray}{text} \\
\verb+\color{silver}{text}+ & \color{silver}{text} \\
\verb+\color{white}{text}+ & \color{white}{text} \\
\hline
\verb+\color{maroon}{text}+ & \color{maroon}{text} \\
\verb+\color{red}{text}+ & \color{red}{text} \\
\verb+\color{yellow}{text}+ & \color{yellow}{text} \\
\verb+\color{lime}{text}+ & \color{lime}{text} \\
\verb+\color{olive}{text}+ & \color{olive}{text} \\
\verb+\color{green}{text}+ & \color{green}{text} \\
\verb+\color{teal}{text}+ & \color{teal}{text} \\
\verb+\color{aqua}{text}+ & \color{aqua}{text} \\
\verb+\color{blue}{text}+ & \color{blue}{text} \\
\verb+\color{navy}{text}+ & \color{navy}{text} \\
\verb+\color{purple}{text}+ & \color{purple}{text} \\ 
\verb+\color{fuchsia}{text}+ & \color{magenta}{text} \\
\hline
\end{array}
$$

[HTML色彩快速参考手册](http://www.w3schools.com/html/html_colors.asp)

### 公式标记与引用
使用`\tag{yourtag}`来标记公式，如果想在之后引用该公式，则还需要加上`\label{yourlabel}`在`\tag`之后，如：
$$ a := x^2-y^3 \tag{\*}\label{\*} $$
为了引用公式，可以使用`\eqref{rlabel}`，如：
$$a+y^3 \stackrel{\eqref{\*}}= x^2$$

可以看到，通过超链接可以跳转到被引用公式位置。

> Written with Macdown.

### 本文转载自 [http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/](http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/)
