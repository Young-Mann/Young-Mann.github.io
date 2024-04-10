---
title: "Functional Analysis tools used in FEM"
date: 2024-04-10T16:11:55+02:00
tags:
- 期权定价
- 我小子又在抄教材了
toc: true
draft: false
---

This is a brief introduction to the mathematical concepts in functional analysis,  which are used in the variational formulation in finite element methods to solve the PDE. The content below is from the page 11-12 and page 27-31 in the book *Computational Methods for Quantitative Finance: Finite Element Methods for Derivative Pricing*[^1].

## Function Spaces
### Definition of partial derivatives
The variational formulation in the finite element method requires tools from functional analysis, in particular Hilbert spaces. Let $G$ be a non-empty open subset of $\mathbb{R}^d$. If a function $u: G \to \mathbb{R}$ is sufficiently smooth, we denote the partial derivatives of $u$ by
$$
D^{\mathbf{n}} u(x):=\frac{\partial^{|\mathbf{n}|} u(x)}{\partial x _1^{n _1} \cdots \partial x _d^{n _d}}=\partial _{x _1}^{n _1} \cdots \partial _{x _d}^{n _d} u(x), \quad x=\left(x _1, \ldots, x _d\right) \in G,
$$
where $\mathbf{n}=\left(n _1, \ldots, n _d\right) \in \mathbb{N} _0^d$ is a multi-index. The order of the partial derivative is given by $|\mathbf{n}|=\sum _{i=1}^d n _i$. 

### Definition of $C^n(G)$ space
For any integer $n \in \mathbb{N} _0$, we define

$$
C^n(G)=\left\\{u: D^{\mathbf{n}} u \text { exists and is continuous on } G \text { for }|\mathbf{n}| \leq n\right\\},
$$
and set $C^{\infty}(G)=\bigcap _{n \geq 0} C^n(G)$. The support of $u$ is denoted by $\operatorname{supp} u$, and we define $C _0^n(G), C _0^{\infty}(G)$ consisting of all functions $u \in C^n(G), C^{\infty}(G)$ with compact support supp $u \Subset G$.

### Definition of $L^p(G)$ space
We denote by $L^p(G), 1 \leq p \leq \infty$ the usual space which consists of all Lebesgue measurable functions $u: G \rightarrow \mathbb{R}$ with finite $L^p$-norm,
where ess sup means the essential supremum disregarding values on nullsets. The case $p=2$ is of particular interest. The space $L^2(G)$ is a Hilbert space with respect to the inner product $(u, v)=\int _G u(x) v(x) \mathrm{d} x$.

### Riesz representation theorem
Let $\mathcal{H}$ be a Hilbert space with the inner product $(\cdot, \cdot) _{\mathcal{H}}$ and norm $\\|u\\| _{\mathcal{H}}:=$ $(u, u) _{\mathcal{H}}^{1 / 2}$. We denote by $\mathcal{H}^\*$ the dual space of $\mathcal{H}$ which consists of all bounded linear functionals $u^\*: \mathcal{H} \rightarrow \mathbb{R}$ on $\mathcal{H}$. $\mathcal{H}^\*$ can be identified with $\mathcal{H}$ by the Riesz representation theorem.

**Theorem** (Riesz representation theorem) For each $u^\* \in \mathcal{H}^\*$ there exists $a$ unique element $u \in \mathcal{H}$ such that
$$
\left\langle u^\*, v\right\rangle_{\mathcal{H}^\*, \mathcal{H}}=(u, v)_{\mathcal{H}} \quad \forall v \in \mathcal{H} .
$$

The mapping $u^\* \mapsto u$ is a linear isomorphism of $\mathcal{H}^*$ onto $\mathcal{H}$.

### Definition of $L^p(J ; \mathcal{H})$ space
The theory of parabolic partial differential equations requires the introduction of Hilbert space-valued $L^p$-functions. As above, let $\mathcal{H}$ be a Hilbert space with the norm $\\|\cdot\\| _{\mathcal{H}}$. Denote by $J$ the interval $J:=(0, T)$ with $T>0$, and let $1 \leq p \leq \infty$. The space $L^p(J ; \mathcal{H})$ is defined by

$$
L^p(J ; \mathcal{H}):=\left\\{u: \bar{J} \rightarrow \mathcal{H} \text { measurable }:\\|u\\| _{L^p(J ; \mathcal{H})}<\infty\right\\},
$$

with the norm
$$
\\|u\\| _{L^p(J ; \mathcal{H})}:= \begin{cases}\left(\int _J\\|u(t)\\| _{\mathcal{H}}^p \mathrm{~d} t\right)^{1 / p} & \text { if } 1 \leq p<\infty \\\ \operatorname{ess~sup} _J\\|u(t)\\| _{\mathcal{H}} & \text { if } p=\infty .\end{cases}
$$
Furthermore, for $n \in \mathbb{N}_0$ let $C^n(J ; \mathcal{H})$ be the space of $\mathcal{H}$-valued functions that are of the class $C^n$ with respect to $t$.

## Sobolev Spaces

We now introduce some particular Hilbert spaces, which consist of functions that are square integrable together with their partial derivatives up to a certain order.

### Definition of weak derivatives
Let $G=(a, b) \subset \mathbb{R}$ be an open, possibly unbounded domain and let first $u \in$ $C^1(\bar{G})$. Integration by parts yields
$$
\int _G u^{\prime} \varphi \mathrm{d} x=-\int _G u \varphi^{\prime} \mathrm{d} x, \quad \forall \varphi \in C _0^1(G) .
$$

If $u \in L^2(G)$, then $u^{\prime}$ does not necessarily exist in the classical sense, but we may define $u^{\prime}$ to be the linear functional
$$
u^\*(\varphi)=-\int _G u \varphi^{\prime} \mathrm{d} x, \quad \forall \varphi \in C _0^1(G) .
$$

This functional is said to be a generalized or weak derivative of $u$. When $u^\*$ is bounded in $L^2(G)$, it follows from Riesz representation theorem that there exists a unique function $w \in L^2(G)$ such that $u^\*(\varphi)=(w, \varphi)$ for all $\varphi \in L^2(G)$, in particular
$$
-\int _G u \varphi^{\prime} \mathrm{d} x=\int _G w \varphi \mathrm{d} x, \quad \forall \varphi \in C _0^1(G) .
$$

We then say that the weak derivative belongs to $L^2(G)$ and write $u^{\prime}=w$. In particular, if $u \in C^1(\bar{G})$, the generalized derivative $u^{\prime}$ coincides with the classical derivative $u^{\prime}$. In a similar way, we can define weak derivatives $D^n u$ of higher order $n \in \mathbb{N}$.

**Definition:** The linear functional $D^n u, n \in \mathbb{N}$ is a weak derivative of $u$ if
$$
\int _G D^n u \varphi \mathrm{d} x=(-1)^n \int _G u D^n \varphi \mathrm{d} x, \quad \forall \varphi \in C _0^n(G) .
$$

### Definition of $H^m(G)$ space
We can now define the spaces $H^m(G)$.

**Definition:** Let $m \in \mathbb{N}$. $H^m(G)$ is the space of all functions whose weak partial derivatives of order $\leq m$ belong to $L^2(G)$​, i.e.
$$
H^m(G)=\left\\{u \in L^2(G): D^n u \in L^2(G) \text { for } n \leq m\right\\} .
$$

We equip $H^m(G)$ with the inner product
$$
(u, v) _{H^m(G)}=\sum _{n=0}^m\left(D^n u, D^n v\right) _{L^2(G)},
$$
and the corresponding norm
$$
 \\|u \\| _{H^m(G)}^2=(u, u) _{H^m(G)}=\sum _{n=0}^m\left \\|D^n u\right \\| _{L^2(G)}^2 .
$$

We sometimes omit the $(G)$ if the domain is clear from the context. $H^m(G)$ is complete and thus a Hilbert space. The space $H^m(G)$ is an example of a more general class of function spaces, called Sobolev spaces.

### Definition of $W^{m, p}(G)$
**Definition:** Let $p \in \mathbb{N} \cup\\{\infty\\}$. $W^{m, p}(G)$ is the space of all functions whose weak partial derivatives of order $\leq m$ belong to $L^p(G)$, i.e.
$$
W^{m, p}(G)=\left\\{u \in L^p(G): D^n u \in L^p(G) \text { for } n \leq m\right\\} .
$$

We equip $W^{m, p}(G)$ with the norm
$$
 \\|u \\| _{W^{m, p}(G)}^p=\sum _{n=0}^m\left \\|D^n u\right \\| _{L^p(G)}^p .
$$

The normed space $W^{m, p}(G)$ is complete and hence a Banach space for $1 \leq p \leq \infty$. Functions $u \in W^{1, p}(G)$ are "essentially" continuous.

**Theorem:** Let $G$ be bounded and $u \in W^{1, p}(G)$. Then, there exists a continuous function $\tilde{u} \in C^0(\bar{G})$ such that $u=\tilde{u}$ a.e. on $G$ and for all $x _1, x _2 \in \bar{G}$ there holds
$$
\tilde{u}\left(x _2\right)-\tilde{u}\left(x _1\right)=\int _{x _1}^{x _2} u^{\prime}(\xi) \mathrm{d} \xi .
$$

We will also need spaces with boundary conditions where we impose $u=0$ on $\partial G$.

### Definition of  $W _0^{1, p}(G)$

**Definition:** Let $1 \leq p<\infty$. Then, $W _0^{1, p}$ is the closure of $C _0^1$ in the $W^{1, p _{-}}$ norm,
$$
W _0^{1, p}(G)=\overline{C _0^1(G)}{ }^{ \\|\cdot \\| _W{ }^{1, p}(G)} .
$$

The space $W _0^{1, p}(G) \subset W^{1, p}(G)$ is a closed linear subspace. In particular, $H _0^1(G):=W _0^{1,2}(G)$ is again a Hilbert space with the norm $ \\|\cdot \\| _{H^1(G)}$. We have the important Poincaré inequality.

### Poincaré inequality
**Theorem(Poincaré inequality):** Assume that $G \subset \mathbb{R}$ bounded. Then,
(1) There exists a constant $C(|G|, p)>0$ such that
$$
\\|u \\| _{L^p(G)} \leq C\left \\|u^{\prime}\right \\| _{L^p(G)}, \quad \forall u \in W _0^{1, p}(G) .
$$
(2) Define
$$
W _\*^{1, p}(G):=\left\\{u \in W^{1, p}(G): \int _G u \mathrm{~d} x=0\right\\} .
$$

Then, (1) holds also for all $u \in W _\*^{1, p}(G)$, with different $C$.

In Chap. 2, we have already introduced the Bochner spaces $L^p(J ; \mathcal{H})$ which consist of functions $u: J \rightarrow \mathcal{H}$ such that the $L^p(J ; \mathcal{H})$-norm is finite. For the theory of parabolic PDEs, it will prove essential to consider maps $u: J \rightarrow \mathcal{H}$ which are also differentiable (in time). We call $u^{\prime}$ the weak derivative of $u$ if
$$
\int _J u^{\prime}(t) \varphi(t) \mathrm{d} t=-\int _J u(t) \varphi^{\prime}(t) \mathrm{d} t, \quad \forall \varphi \in C _0^1(J) .
$$

### Definition of $W^{1, p}(J ; \mathcal{H})$
**Definition:** Let $\mathcal{H}$ be a real Hilbert space with the norm $ \\|\cdot \\| _{\mathcal{H}}$. For $J=(0, T)$ with $T>0$, and $1 \leq p \leq \infty$, the space $W^{1, p}(J ; \mathcal{H})$ is defined by
$$
W^{1, p}(J ; \mathcal{H}):=\left\\{u \in L^p(J ; \mathcal{H}): u^{\prime} \in L^p(J ; \mathcal{H})\right\\},
$$
with the norm

$$
\\|u\\| _{W^{1, p}(J ; \mathcal{H})}:= \begin{cases}\left(\int _J\\|u(t)\\| _{\mathcal{H}}^p+\left\\|u^{\prime}(t)\right\\| _{\mathcal{H}}^p \mathrm{~d} t\right)^{1 / p} & \text { if } 1 \leq p<\infty, \\\ \operatorname{ess} \sup  _J\left(\\|u(t)\\| _{\mathcal{H}}+\left\\|u^{\prime}(t)\right\\| _{\mathcal{H}}\right) & \text { if } p=\infty .\end{cases}
$$

We again denote by $H^1(J ; \mathcal{H}):=W^{1,2}(J ; \mathcal{H})$.

[^1]: Hilber, Norbert, Oleg Reichmann, Christoph Schwab, and Christoph Winter. *Computational Methods for Quantitative Finance: Finite Element Methods for Derivative Pricing*. Springer Finance. Berlin, Heidelberg: Springer Berlin Heidelberg, 2013. https://doi.org/10.1007/978-3-642-35401-4.