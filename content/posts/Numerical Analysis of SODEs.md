Numerical Analysis of SODEs



Definition 1.2.1 (Generalized inverse distribution function associated to a distribution function). Let $F: \mathbb{R} \rightarrow[0,1]$ be a distribution function. Then we denote by $I_F:(0,1) \rightarrow$ $\mathbb{R}$ the function which satisfies for all $y \in(0,1)$ that

$$
I_F(y)=\inf \{x \in \mathbb{R}: F(x) \geq y\}=\inf \left(F^{-1}([y, 1])\right)
$$

and we call $I_F$ the generalized inverse distribution function associated to $F$.
Definition 1.2.1 plays a key role in the inversion method which we present in Theorem 1.2 .7 below. In the following we present a few comments regarding Definition 1.2.1.
(i) Let $F: \mathbb{R} \rightarrow[0,1]$ be a distribution function. Then note that for every $y \in(0,1)$ it holds that the set
$$
\{x \in \mathbb{R}: F(x) \geq y\}
$$

(see Definition 1.2.1) is not empty and bounded from below. Indeed, the fact that $F$ satisfies

$$
\lim _{x \rightarrow \infty} F(x)=1
$$

ensures that for every $y \in(0,1)$ it holds that $\{x \in \mathbb{R}: F(x) \geq y\}$ is not empty. Moreover, the fact that $F$ satisfies

$$
\lim _{x \rightarrow-\infty} F(x)=0
$$

implies that for every $y \in(0,1)$ it holds that $\{x \in \mathbb{R}: F(x) \geq y\}$ is bounded from below. Therefore, the function $I_F:(0,1) \rightarrow \mathbb{R}$ in Definition 1.2 .1 is well defined.
(ii) Let $F: \mathbb{R} \rightarrow[0,1]$ be a distribution function. The generalized inverse distribution function associated to $F$ is sometimes also referred to as quantile function associated to $F$ (cf. Item (iii) below).
(iii) Let $(\Omega, \mathcal{F}, P)$ be a probability space, let $X: \Omega \rightarrow \mathbb{R}$ be an $\mathcal{F} / \mathcal{B}(\mathbb{R})$-measurable function (we think of $X$ as a model for the change of the value of a given portfolio of financial assets within a given time period), let $F: \mathbb{R} \rightarrow[0,1]$ be the distribution function of $-X$ and let $\alpha \in(0,1)$ (we think of $\alpha$ as a confidence level for the statement below, which is typically a number close to 1 such as $99 \%$ or $95 \%$, and we think of $1-\alpha$ as a small number such as $1 \%$ or $5 \%$ ). Then the real number

$$
\begin{aligned}
I_F(\alpha) & =\min \{x \in \mathbb{R}: F(x) \geq \alpha\}=\min \{x \in \mathbb{R}: P(-X \leq x) \geq \alpha\} \\
& =\min \{x \in \mathbb{R}: P(\neg[-X>x]) \geq \alpha\} \\
& =\min \{x \in \mathbb{R}: P(-X>x) \leq 1-\alpha\}
\end{aligned}
$$

is sometimes referred to as the value at risk with confidence level $\alpha$ associated to $-X$ ( $V a R$ with confidence level $\alpha$ associated to $-X$ ) in the financial risk management literature. Observe that (1.5) ensures that

$$
P\left(-X \leq I_F(\alpha)\right)=P\left(\neg\left[-X>I_F(\alpha)\right]\right) \geq \alpha \text {. }
$$


It thus holds with a probability of at least $\alpha$ that the loss $-X$ will not exceed the number $I_F(\alpha)$ (the value at risk with confidence level $\alpha$ ).







Lemma 1.2 .4 (Properties of generalized inverse distribution functions). Let $F: \mathbb{R} \rightarrow$ $[0,1]$ be a distribution function and let $I_F:(0,1) \rightarrow \mathbb{R}$ be the generalized inverse distribution function associated to $F$. Then $I_F$ fulfills the following properties:
(i) $I_F$ is non-decreasing, i.e., for all $y_1, y_2 \in(0,1)$ with $y_1 \leq y_2: I_F\left(y_1\right) \leq I_F\left(y_2\right)$,
(ii) for all $y \in(0,1): F\left(I_F(y)\right) \geq y, F^{-1}([y, 1])=\left[I_F(y), \infty\right)$, and $I_F(y)=$ $\min \left(F^{-1}([y, 1])\right)$
(iii) for all $x \in F^{-1}((0,1))=\{z \in \mathbb{R}: F(z) \in(0,1)\}: I_F(F(x)) \leq x$,
(iv) for all $x \in \mathbb{R}, y \in(0,1): I_F(y) \leq x$ if and only if $y \leq F(x)$ (switching formula),
(v) for all $x \in \mathbb{R}, y \in(0,1): I_F(y)>x$ if and only if $y>F(x)$ (switching formula), and
(vi) for all open sets $D \subseteq \mathbb{R}$ with the property that $\left.F\right|_D: D \rightarrow[0,1]$ is injective and all $y \in F(D):\left.F\right|_D ^{-1}(y)=I_F(y)$





Lemma 1.2.5 (Continuity properties of generalized inverse distribution functions). Let $F: \mathbb{R} \rightarrow[0,1]$ be a distribution function and let $I_F:(0,1) \rightarrow \mathbb{R}$ be the generalized inverse distribution function associated to $F$. Then $I_F$ fulfills the following properties:
(i) $I_F$ is a càglàd (continue à gauche, limitée à droite) function, i.e., for all $y \in$ $(0,1): \lim _{z \nearrow y} I_F(z)$ and $\lim _{z \searrow y} I_F(z)$ exist and $\lim _{z \nearrow y} I_F(z)=I_F(y)$,
(ii) for all $y \in(0,1):\left[I_F(y), \lim _{z \backslash y} I_F(z)\right) \subseteq F^{-1}(\{y\}) \subseteq\left[I_F(y), \lim _{z \searrow y} I_F(z)\right]$,
(iii) for all $y \in(0,1): I_F$ is continuous in $y \in(0,1)$ if and only if $\#_{\mathbb{R}}\left(F^{-1}(\{y\})\right)=$ $\#_{\mathbb{R}}(\{x \in \mathbb{R}: F(x)=y\}) \leq 1$.



Theorem 1.2.7 (Inversion method). Let $F: \mathbb{R} \rightarrow[0,1]$ be a distribution function, let $(\Omega, \mathcal{F}, P)$ be a probability space, and let $U: \Omega \rightarrow \mathbb{R}$ be an $\mathcal{U}_{(0,1)}$-distributed random variable with $U(\Omega) \subseteq(0,1)$. Then $F$ is the distribution function of the $\mathcal{F} / \mathcal{B}(\mathbb{R})$-measurable function $I_F(U)=I_F \circ U: \Omega \rightarrow \mathbb{R}$, i.e., it holds for all $x \in \mathbb{R}$ that

$$
P\left(I_F(U) \leq x\right)=F(x)
$$