---
title: "基于特征函数推导概率分布函数"
date: 2024-03-22T14:59:52+01:00
tags:
- 概率论
toc: true
draft: false
---
## 引言

最近，笔者在某节课上学习了<u>利用特征函数及FFT的期权定价方法</u>，which 需要用到特征函数反推概率分布函数，也就是下面这个等式：
$$
F(x) = \frac {1}{2} +  \frac {1} {2\pi}\int ^{\infty} _{0} \frac {e^{itx} \phi(-t) - e^{-itx}\phi(t)} {it} \text{d}t,
$$
其中，$F(x)=F_Z(x)$是关于随机变量$Z$的概率分布函数，$\phi(t)=\phi_Z(t) :=\mathbb{E}[e^{itZ}] =  \int^{+\infty} _{-\infty}e^{itz}\text{d}F(z)$ 是关于随机变量$Z$的特征函数，$i:=\sqrt{-1}$。

然而，这节课的课件里没有这个表达式的推导过程。为了理解这个式子从何而来，笔者找到了[这篇文章](https://doi.org/10.1093/biomet/38.3-4.481)[^1]，将推导思路记了下来，写作本文。

为简化符号，下文将继续省略概率分布函数、特征函数关于随机变量的下标。

另外，笔者不确定下文中自己对「为什么可以交换积分次序」的证明是对是错。如果有大佬能指出其中的问题，那就在此提前感谢了。

## 推导过程

第一步，任取$0<\varepsilon<\lambda$，硬凑积分如下：
$$
\begin{align}
\text{I}&= \frac {1} {\pi}\int ^\lambda _{\varepsilon} \frac {e^{itx} \phi(-t) - e^{-itx}\phi(t)} {it} \text{d}t
\nonumber
\\\
&=  \frac {1} {\pi} \int ^\lambda _{\varepsilon} \frac {e^{itx} \int^{+\infty} _{-\infty} e^{-itz} \text{d}F(z) - e^{-itx}\int^{+\infty} _{-\infty}e^{itz}\text{d}F(z)} {it} \text{d}t
\nonumber
\\\
&= \frac{1}{\pi} \int ^\lambda _{\varepsilon}  \int^{+\infty} _{-\infty} \frac {e^{i[t(x-z)]}- e^{i[-t(x-z)]}} {it}\text{d}F(z) \text{d}t 
\\\
&\xlongequal{\text{使用欧拉公式}} \frac{2}{\pi}  \int ^{\lambda} _{\varepsilon} \int^{+\infty} _{-\infty} \frac {\sin(t(x-z))}{t} \text{d}F(z) \text{d}t 
\\\
&\xlongequal{\text{积分换序}}\int ^{+\infty} _{-\infty} \text{d}F(z) \frac{2}{\pi} \int ^{\lambda} _{\varepsilon}   \frac {\sin(t(x-z))}{t} \text{d}t
\end{align}
$$

<details class="notes">
<summary>如何从(1)式推出(2)式？</summary>
<p>
这里，从$(1)$式推出$(2)$式需使用欧拉公式（即$e^{iu}=\cos(u)+i\sin(u), \forall u \in \mathbb{R}$）展开$e^{i[t(x-z)]}$及$e^{i[-t(x-z)]}$即可：
$$
\begin{align*}
(1) 式 &=\frac{1}{\pi} \int ^\lambda _{\varepsilon}  \int^{+\infty} _{-\infty}  \frac {\cos(t(x-z)) + i\sin(t(x-z)) -\cos(-t(x-z)) - i\sin(-t(x-z))}{it} \text{d}F(z) \text{d}t 
\\\
&=  \frac{2}{\pi}  \int ^{\lambda} _{\varepsilon} \int^{+\infty} _{-\infty} \frac {\sin(t(x-z))}{t} \text{d}F(z) \text{d}t 
\end{align*}.
$$
</p>
</details>

<details class="notes">
<summary>如何从(2)式推出(3)式？</summary>
<p>
若要交换积分次序，我们需要证明被积函数 $ f(t,z) := \frac{\sin(t(x-z))}{t}$ 在 $( \varepsilon ,  \lambda) \times(-\infty, +\infty) $ 上绝对可积。根据假设可知，$0 < \varepsilon < t < \lambda$，且 $-\infty < z < +\infty$。那么，我们有：
$$
\begin{align*}
\left | \frac{\sin(t(x-z))}{t} \right |& < \left | \frac{\sin(t(x-z))}{\varepsilon} \right |
\\\
&= \frac { \left | \sin(t(x-z)) \right |} {\varepsilon} 
\\\
&\leq \frac 1 \varepsilon.
\end{align*}
$$
</p>




<p>
因此，我们有：

$$
\begin{align*}
\int ^{\lambda} _{\varepsilon} \int^{+\infty} _{-\infty} \frac {\sin(t(x-z))}{t} \text{d}F(z) \text{d}t &< \int ^{\lambda} _{\varepsilon} \int^{+\infty} _{-\infty} \frac {1}{\varepsilon} \text{d}F(z) \text{d}t
\\\
&= \int ^{+\infty} _{-\infty} \text{d}F(z) \int ^{\lambda} _{\varepsilon} \frac {1}{\varepsilon} \text{d}t
\\\
&= {1} \cdot \frac {\lambda - \varepsilon} {\varepsilon} < \infty.
\end{align*}
$$

由此，我们证明了 $ \left | f(t,z) \right | = \left | \frac{\sin(t(x-z))}{t} \right |$  在 $( \varepsilon ,  \lambda) \times(-\infty, +\infty) $ 上的积分值有限，因此 $ \frac{\sin(t(x-z))}{t}$ 绝对可积，可以交换积分次序。
</p>
</details>


第二步，为了求解$(3)$式，我们需要定义函数如下：
$$
\begin{align*} 
\operatorname{sign}(x-z) = \frac {2} {\pi} \int ^{\infty} _{0}  \frac{\sin(t(x-z))}{t} \text{d}t &= 
\begin{cases} 
-1, & 如果 z>x
\\\ 0, & 如果 z=x
\\\ 1, & 如果 z<x
\end{cases}.
\end{align*}
$$

最后，第三步，当$\varepsilon \to 0$ 且 $\lambda \to \infty$时，有以下左右两式相等：
$$
\begin{align}
 \lim\limits _{\varepsilon \to 0 \atop \lambda \to \infty} \text{I} 
&=\lim\limits _{\varepsilon \to 0 \atop \lambda \to \infty} \text{(3)式}
\nonumber
\\\
\Longrightarrow
\quad
\frac {1} {\pi}\int ^{\infty} _{0} \frac {e^{itx} \phi(-t) - e^{-itx}\phi(t)} {it} \text{d}t &= \int ^{+\infty} _{-\infty} \text{d}F(z)\thinspace\lim\limits _{\varepsilon \to 0 \atop \lambda \to \infty} \frac{2}{\pi} \int ^{\lambda} _{\varepsilon}   \frac {\sin(t(x-z))}{t} \text{d}t
\end{align}
$$




$$
\begin{align}
\text{RHS}
&=\int ^{+\infty} _{-\infty} \text{d}F(z)\thinspace\lim\limits _{\varepsilon \to 0 \atop \lambda \to \infty} \frac{2}{\pi} \int ^{\lambda} _{\varepsilon}   \frac {\sin(t(x-z))}{t} \text{d}t \nonumber
\\\ \nonumber
&= \int ^{+\infty} _{-\infty} \operatorname{sign}(x-z)\thinspace \text{d}F(z)
 \\\ \nonumber
&= \int ^{+\infty} _{x} (-1) \thinspace \text{d}F(z) + \int ^{x} _{-\infty} (1) \thinspace \text{d}F(z) 
 \\\ \nonumber
&= -(F(z))| _{x}^{\infty} + (F(z))| ^{x} _{-\infty} 
 \\\ \nonumber
&= -[1-F(x)] + [F(x)-0]
 \\\
&=2F(x)-1
\end{align}
$$

将$(5)$式代入$(4)$式，可得：
$$
\begin{align}
F(x) = \mathbb{Q}(X \leq x) &= \frac {1}{2} +  \frac {1} {2\pi}\int ^{\infty} _{0} \frac {e^{itx} \phi(-t) - e^{-itx}\phi(t)} {it} \text{d}t
\\\
&= \frac {1}{2} +  \frac {1} {\pi}\int ^{\infty} _{0} \frac {\overline { -e^{-itx} \phi(t)} - e^{-itx}\phi(t)} {2it } \text{d}t
\\\
&= \frac {1}{2} -  \frac {1} {\pi}\int ^{\infty} _{0} \operatorname{Re} \left\[ \frac { e^{-itx}\phi(t)} {2it } \right\] \text{d}t
\end{align}
$$

因此，
$$
\begin{align}
\mathbb{Q}(X>x) = 1 - F(x) &=  \frac {1}{2} +  \frac {1} {\pi}\int ^{\infty} _{0} \operatorname{Re} \left\[ \frac { e^{-itx}\phi(t)} {2it } \right\] \text{d}t
\end{align}
$$


[^1]: J. GIL-PELAEZ, Note on the inversion theorem, Biometrika, Volume 38, Issue 3-4, December 1951, Pages 481–482, https://doi.org/10.1093/biomet/38.3-4.481[