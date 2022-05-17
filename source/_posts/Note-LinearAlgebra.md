---
title: 线代笔记
categories: Linear Algebra
mathjax: true
---

看的GS的课 [B站上有很多](https://www.bilibili.com/video/BV1bb411H7JN?p=3&spm_id_from=pageDriver)

记录一下学的时候产生的问题和搜到的、思考的答案，顺便练一下 $\LaTeX$

抛开这些，别的内容就是存粹给自己看的、不需要了之后就会删掉

<!-- more -->

# $\S 1$ 方程组的几何解释

没问题产生，略

# $\S 2$ 矩阵消元

同上

# $\S 3$ 矩阵的乘法和逆


## Q1. 如何证明矩阵乘法的结合律

待填坑

---

## Q2. 如何理解矩阵的分块相乘

第三节讲的第五种矩阵乘法方法

看的时候并没有像弹幕一样一眼理解 人比较笨。。

令矩阵 $A$ 分为 $\begin{bmatrix} A_1&A_2\\ A_3&A_4\\ \end{bmatrix}$， $B$ 分为 $\begin{bmatrix} B_1&B_2 \\ B_3&B_4\\ \end{bmatrix}$，$C=AB=$ $\begin{bmatrix}A_1B_1+A_2B_3&A_1B_2+A_2B_4\\A_3B_1+A_4B_3&A_3B_2+A_4B_4 \end{bmatrix}$

当然分出来的小矩阵 $A_i, B_i$ 之间满足矩阵乘法的要求，即 $A$ 列的划分和 $B$ 行的划分相同

以 $A=n\times m=$$\begin{bmatrix}1&2&|&3\\4&5&|&6\\-&-&-&-\\7&8&|&9 \end{bmatrix}$ , $B=m\times p=$$\begin{bmatrix}1&|&2\\3&|&4\\-&-&-\\5&|&6 \end{bmatrix}$为例，**先考虑 $A$ 只划分列，$B$ 只划分行时候的情况，即 $m$ 不变**

$\begin{bmatrix}1&2&|&3\\4&5&|&6\end{bmatrix}$$\begin{bmatrix}1\\3\\-\\5\end{bmatrix}=\begin{bmatrix}1&2\\4&5\end{bmatrix}\begin{bmatrix}1\\3\end{bmatrix}+\begin{bmatrix}3\\6\end{bmatrix}\begin{bmatrix}5\end{bmatrix}$

这个过程过程意味着用 $\begin{bmatrix}1\\3\\5\end{bmatrix}$ 作为系数，对 $column\ vector$$\begin{bmatrix}1\\4\end{bmatrix}$ $\begin{bmatrix}2\\5\end{bmatrix}$ $\begin{bmatrix} 3\\6\end{bmatrix}$ 进行线性组合，**所以 $A$ 划分行  $B$ 划分列的运算是成立的，即加法的结合律**

这样重复下去，$A$ 只有列的划分 最终分为 $m$ 行，$B$ 中只有行的划分，最终分为 $p$ 列，线性组合得到一个 $m\times p$ 的矩阵

同样，用矩阵分块去理解前四种方法，也是很显然的事情

-----

## Q3. 如何证明 $square\ matrix$ 左逆等于右逆

待填坑

# $\S 4\ A$ 的 $LU$ 分解

## P1. 转置矩阵

上来一个 **转置矩阵** ，查了维基百科：

在线性代数中，矩阵 $A$ 的转置是另一个矩阵 $A^T$，由下列等价动作建立

* 把 $A$ 的列写作 $A^T$ 的行
* 把 $A$ 的行写作 $A^T$ 的列

还看了一些性质，等到之后有知识储备了再弄吧

## Q1. 为什么对于置换矩阵 $P$ 有 $P^{-1}\ =\ P^T$

考虑一个置换矩阵 $P = \begin{bmatrix}
0 & 0 & 1 \\
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}$， 如果要求一个 $P^{-1}$ 使得 $P^{-1}P=I$，那么对于 $I$ 的第一个主元，由 $P^{-1}$ 的第一行和 $P$ 的第一列点乘得到，显然只有 $\begin{bmatrix}
0 & 1 & 0
\end{bmatrix}$$\times$$\begin{bmatrix}0\\1\\0\end{bmatrix}$得到 $1$，别的都会是 $0$，对于别的主元也是一样，因此在这个例子里很容易得到 $P^{-1}=\begin{bmatrix}
0&1&0\\
0&0&1\\
1&0&0
\end{bmatrix}$，对于所有的置换矩阵都是如此，即 $P^{-1}$ 的第 $i$ 行必须是$P$ 的第 $i$ 列，即有 $P^{-1}=P^T$

-----

## Q2. 关于 $LU$ 分解对比 $Gauss-Jordan$求逆方法

高斯消元的时候，左乘消元矩阵的过程会产生交叉：

$\mathop{\begin{bmatrix}
1 & 0 & 0\\
0 & 1 & 0\\
0 & -5 & 1
\end{bmatrix}}\limits_{E_{32}}\mathop{\begin{bmatrix}
1&0&0\\
-2&1&0\\
0&0&1
\end{bmatrix}}\limits_{E_{21}}=\mathop{\begin{bmatrix}
1&0&0\\
-2&1&0\\
10&-5&1
\end{bmatrix}}\limits_{E}$

其中 $E$ 左下角的10就是由于消元时使用的是新的第二行，因此要加回10倍的第一行

这样的话，一个单位操作算成一个位置上的乘法和减法，整体消元过程就是 $O(N^3)$ 的复杂度

$$1^2+2^2+\dots+n^2=\int_1^nx^2\mathrm{d}x=\frac{1}{3}n^3$$

但是在求解L的时候，可以直接把 $E_{21}^{-1}、E_{32}^{-1}$ 填入， $\mathop{\begin{bmatrix}
1&0&0\\
2&1&0\\
0&0&1
\end{bmatrix}}\limits_{E_{21}^{-1}}
\mathop{\begin{bmatrix}
1&0&0\\
0&1&0\\
0&5&1
\end{bmatrix}}\limits_{E_{32}^{-1}}=
\mathop{\begin{bmatrix}
1&0&0\\
2&1&0\\
0&5&1
\end{bmatrix}}\limits_{L}$

因为这个过程中，在处理第 $i$ 行时依赖的是 $i-1$ 行，倒推不会产生传递，因此每行之间没有影响

除去了矩阵乘法的过程，这个分解使得求逆的复杂度变低

# $\S 5$ 转置、置换、向量空间

$\begin{bmatrix}
1 & 1 & 2\\
2 & 1 & 3\\
3 & 1 & 4\\
4 & 1 & 5
\end{bmatrix}\times\begin{bmatrix}x_1\\x_2\\x_3\end{bmatrix}$ $\iff x_1\begin{bmatrix}1\\2\\3\\4\end{bmatrix}+x_2\begin{bmatrix}1\\1\\1\\1\end{bmatrix}+x_3\begin{bmatrix}2\\3\\4\\5\end{bmatrix}$

wxy说严格来讲一般矩阵乘列向量称为线性组合，抛开翻译不谈，Gilbert课上说的也只是combination..

----

# $\S 6$ 列向量和零空间

听的异常轻松，可能是我有地方自以为理解了其实不然

看书的时候再好好看、好好做题吧，这个不预习可能不太行

----

# $\S 7$ $Ax=0$：主变量、特解

$rref(A)$ 这个矩阵不难理解，就是A消元、回代然后矩阵分块

这个过程我唯一不理解的是为什么可以写成$R=[I\ F]$的形式，然后xy说是行交换，然后我不理解，那$x$的$\begin{bmatrix}x1\\x2\\\vdots \\ x_n\end{bmatrix}$怎么办，然后xy说$x$也交换就好了..

就好了..

---

# $\S 8$ $Ax=b$：可解性和解的结构

