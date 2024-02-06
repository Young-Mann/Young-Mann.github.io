---
title: The Volatility Surface
tags:
description: 
toc: false
series:
date: '2023-12-07T22:14:50+01:00'
---



> 简单记录第一次看 *The Volatility Surface* 时推导有问题的部分。

(P16) 给定下列等式：

$$
\begin{gathered}
\frac{\partial V}{\partial t}+\frac{1}{2} v S^2 \frac{\partial^2 V}{\partial S^2}+ 
\rho \eta v S \frac{\partial^2 V}{\partial S \partial v}+\frac{1}{2} \eta^2 v \frac{\partial^2 V}{\partial v^2}+r S \frac{\partial V}{\partial S}-r V 
-\lambda(v-\bar{v}) \frac{\partial V}{\partial v}=0
\end{gathered}
$$
进行如下的变量代换：
$$
x=\ln \left(F_{t, T} / K\right)=\ln \left(S e^{r \tau} / K\right) ,
$$
可得到如下的等式：
$$
\begin{gathered}
-\frac{\partial C}{\partial \tau}+\frac{1}{2} v \frac{\partial^2 C}{\partial x^2}-\frac{1}{2} v \frac{\partial C}{\partial x}+\frac{1}{2} \eta^2 v \frac{\partial^2 C}{\partial v^2}+\rho \eta v \frac{\partial^2 C}{\partial x \partial v}-\lambda(v-\bar{v}) \frac{\partial V}{\partial v}=0
\end{gathered}
$$
一开始看书时没明白 $r S \frac{\partial V}{\partial S}-r V $ 这项是怎么被消去的。看了[这个回答](https://quant.stackexchange.com/questions/34742/gatherals-change-of-variables-for-stochastic-volatility-pde)之后才发现我没把当期价格$V$表示成终期价格$C$：$e^{-r \tau} C(x, \nu, \tau)=V(S, \nu, t)$，所以一开始推不出书上的式子。