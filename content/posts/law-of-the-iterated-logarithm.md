---
title: "Law of the iterated logarithm"
date: 2023-10-23T13:49:58+02:00
tags:
toc: true
---



今天读 [Mathematical Foundations for Finance](https://metaphor.ethz.ch/x/2023/hs/401-3913-01L/) 讲义里的布朗运动一章时，看到布朗运动的轨道满足一个以前没见过的 Law of the iterated logarithm。具体而言，假设$W=(W_t)_{t \geq 0} 是一个布朗运动，那么我们有：$


$$
\left.\begin{array}{l}
\limsup\limits_{t \rightarrow \infty} \\\
\liminf\limits_{t \rightarrow \infty}
\end{array}
\right\} \frac{W_t}{\psi_{\text {glob }}(t)}=\left\{\begin{array}{l}
+1 \\
-1
\end{array} \quad P\right. \text {-a.s., }
$$
其中 $\psi_{\text {glob }}(t) := \sqrt{2t \log (\log t)}$。
