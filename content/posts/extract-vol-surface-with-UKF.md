---
title: "Extract near-term shape of volatility surface with UKF"
date: 2024-12-30T16:11:15+01:00
tags:
- 期权定价
description: 我部分复现了 Carr and Wu (2016) ，基于假设的 IV 和 OEV 的 dynamics，在无动态套利对应的二次方程的约束下，使用 Unscented Kalman Filter 提取波动率曲面的近期形态。复现结果和原文有较大差异，尤其在 Option Expected Volatility (OEV) 的估计上——我过高估计了 OEV 。
toc: true
draft: false

---

> 这学期的某门 Seminar 要求复现给定范围内的一篇论文。我尝试复现了 Carr and Wu (2016) [^carr-and-wu]的部分结果。
>
> Carr and Wu (2016) 提出了一个被称作 VGVV model [^-1] 的期权定价模型：
>
> 1. 他们假设了风险中性测度下的 underlying price, implied volatility 以及两者 correlation 的 dynamics，并为 implied volatility 指定了更具体的形式。在这 3 个 dynamics 中， **$(m_t, w_t, \eta_t,v_t, \rho_t)$ 这 5 个参数是未知的。**
> 2. 为了分析 Volatility Risk Premium，他们定义了 option realized volatility 和 option expected volatility，并假设了真实世界测度下的 underlying price, implied volatility 以及两者 correlation 的 dynamics。在这 3 个 dynamic 中， **$(m_t^{\mathbb{P}}, w_t, \eta_t,v_t^{\mathbb{P}},\rho_t^{\mathbb{P}})$ 这 5 个参数是未知的。**
> 3. 从市场内不存在 dynamic arbitrage 的要求出发，可推得 put option BSM price 与其 Vega, Gamma, Vanna, Volga之间必须满足的PDE。这个作为约束条件的 PDE 可被化简为关于 implied variance[^-11] (以及 option expected variance) 的二次方程，其中包含  $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ 。**如果已知 $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ 的值，就能计算同一时间点的 implied variance (以及 option expected variance) ，以及对应的期权价格。**
> 4. 那么，如何估计 $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ 的值？为此，他们使用了 Unscented Kalman Filter（简称 UKF）[^ukf]提取每个时间点的 $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ 。需要注意，在使用UKF前，需要估计其 process noise 和 measurement noise 的协方差矩阵。
>
> 下文简单记录了  Carr and Wu (2016) 的思路，以及我的复现结果。

## 1 Introduction

  Carr and Wu (2016) develops a so-called VGVV model for option pricing. The main components of this model includes:

1. Assumptions for the dynamics: The assumptions for the dynamics of underlying price, implied volatility / option expected volatility[^0] , and the return-volatility correlation.
2. A quadratic equation as the no-dynamic-arbitrage constraint on the current shape of volatility surfacec, which shows the relation between the current shape of volatility surface and the current values of certain parameters in the 3 dynamics defined above.
3. A Unscented Kalman Filter to extract the current values of certain parameters in the 3 dynamics defined above, and use this values to estimate the volatility surface in next time step.



1 ## Motivation

Tradition option pricing models focus on the option prices instead of the implied volatility, while institutional investors exchange their quotes not through options prices, but through the implied volatility from the Black-Scholes-Merton model. 
Apart from that, traditional option pricing models require the full specification of the instantaneous variance dynamics, which is not always well-determined, and requires recalibration over time.

In order to handle the implied volatility and alleviate the drawbacks of full-specified instantaneous variance rate dynamics, Carr and Wu (2016) develops an option pricing model, which 
- models the dynamics of the implied and expected volatility, instead of the instantaneous volatility;
- estimates the near-term shape of the volatility surface, only using the current values of the parameters in volatility dynamics without fully specifying it.

## 2 Methodology

### 2.1 Assumptions for the dynamics of BSM implied volatility surface

Carr and Wu (2016) consider a market with a risk-free bond, a risky asset, and a continuum of vanilla European options written on the stock. The dynamics of the underlying risky asset, the implied volatility, and the return-volatility correlation process are shown as follows:
$$
\begin{align}
	\mathrm{d}S_t/S_t&=\sqrt{v_t}\mathrm{d}W_t,\\\
	\mathrm{d}I_t(K,T)&=\mu _t\mathrm{d}t+\omega _t\mathrm{d}Z_t,\\\
	\mathbb{E} _t\left[ dW_tdZ_t \right] &=\rho _t\mathrm{d}t,\\\
\end{align} 
$$

### 2.2 Definition of ORV and OEV

Carr and WU (2016) constructs the new concept of realized and expected volatilities, which are defined in reference to a specific option contract. 


**Definition of ORV:** Let $\operatorname{ORV}(K,t,T)$ denotes the option realized volatility corresponding to the option contract which starts at $t$, struck at $K$, and expires at $T$, and let $\{t_j\}_{j=0}^N$ be the sequence of days during the life of the option, where $t_0=t$ and $t_N=T$, the $\operatorname{ORV}(K,t,T)$  can be computed as follows:

$$
\begin{align}
\text{ORV}(K, t, T) := &x
\\\
\text { s.t. } &\quad 0=  B\left(S_T, x, T\right)-B\left(S_t, x, t\right) \ -\sum_{j=1}^N B_S\left(S_{t_{j-1}}, x, t_{j-1}\right)\left(S_{t_j}-S_{t_{j-1}}\right),
\end{align}
$$
where $B(S_{t_j} , x, t_j )$ denotes the BSM value of the option contract on date $t_j$, and $B_S(S_{t_j} , x, t_j )$ denotes the corresponding BSM delta of the contract.

Unlike the volatility, the ORV is not fully realized until time $T$. Therefore, the ex post realized P&L from buying an option contract and performing delta-hedging on it in the time interval $[t,T]$ is the difference between the BSM value of option with the ORV input and that with the implied volatility input:

$$
\operatorname{P\\&L}(t, T)=B(S, \operatorname{ORV}(K, t, T), t )-B\left(S, I_t(K, T), t\right) .
$$

**Definition of OEV: **
Taking expectation on both sides of the ex post realized P&L, the ex ante expected volatility risk premium can thus be computed. For convenience of computing ex ante volatility risk premium, the option expected volatility (OEV) is constructed. The option expected volatility is the volatility input such that the expected $P\&L$ is zero if one buys this option and delta-hedge to the maturity date:
$$
\begin{align}
\operatorname{OEV}(K, t, T) :=  &V_t(K,T)  \\\
\text { s.t. } &B\left(S, V_t(K, T), t ; K, T\right)=\mathbb{E}_t^{\mathbb{P}}[B(S, \operatorname{ORV}(K, t, T), t ; K, T)]
\end{align}
$$

Therefore, the volatility risk premium (VRP) from buying this option can be computed as:
$$
\operatorname{VRP}_t(K, T)=B\left(S, V_t(K, T), t ; K, T\right)-B\left(S, I_t(K, T), t ; K, T\right).
$$



### 2.3 The no-dynamic-arbitrage constraint on IV surface

Carr and Wu (2016) specifies the no-arbitrage constraints on the market. To avoid static arbitrage, the BSM implied volatility is required to be positive. To avoid dynamic arbitrage, the BSM put price $B(S,\sigma, t)$ and the BSM implied volatility is required to satisfy the following PDE[^1]

$$
-B_t=\mu_t {B_\sigma}+\frac{1}{2} v_t S_{{t}}^2 B_{S S}+\rho_t \omega_t \sqrt{v_t} {S_t} B_{S \sigma}+\frac{1}{2} \omega_t^2 {B_{\sigma \sigma}} ,
$$
where the subscripts of B denote the partial derivatives of B. By plugging those partial derivatives into the PDE above and denote that $k := \ln(K/S), \tau := T-t$, we can rewrite the IV function $I_t(K,T)$ as $I_t(k,\tau)$ and derive the no-dynamic-arbitrage constraint equation:

$$
\begin{align}
\frac{1}{2} I_t^2-\mu_t I_t \tau-\frac{1}{2} v_t-\rho_t \frac{\omega_t}{I_t} \sqrt{v_t}\left(k+\frac{I_t^2 \tau}{2}\right) -\frac{1}{2} \frac{\omega_t^2}{I_t^2}\left(k^2-\frac{1}{4} I_t^4 \tau^2\right) = 0
\end{align}
$$

Different parameterizations for $\mu_t$ and $\omega_t$ in the dynamics of the implied volatility dynamics lead to different constraints on the current shape of the implied volatility. Carr and Wu (2016) use a proportional configuration for the IV dynamics:

$$
d I_t(K, T) / I_t(K, T)=e^{-\eta_t(T-t)}\left(m_t d t+w_t d Z_t\right), \quad w_t, \eta_t>0.
$$

Thus, the no-dynamic-arbitrage constraint can be rewritten as:
$$
 \frac{1}{4} e^{-2 \eta_t \tau} w_t^2 \tau^2 I_t^4
 +\left(1-2 e^{-\eta_t \tau} m_t \tau-e^{-\eta_t \tau} w_t \rho_t \sqrt{v_t} \tau\right) I_t^2 
 -\left(v_t+2 e^{-\eta_t \tau} w_t \rho_t \sqrt{v_t} k+e^{-2 \eta_t \tau} w_t^2 k^2\right) = 0
$$


Given a fixed time to maturity, this equation describes a hyperbola in the variables $I_t^2$:
$$
I_t^2(k)=a_t+\frac{2}{\tau} \sqrt{\left(k-b_t\right)^2+c_t},
$$
with 
$$
\begin{aligned}
a_t & =\frac{-2\left(1-2 e^{-\eta_t \tau} m_t \tau-e^{-\eta_t \tau} w_t \rho_t \sqrt{v_t} \tau\right)}{e^{-2 \eta_t \tau} w_t^2 \tau^2}, \\\
b_t & =-\frac{\rho \sqrt{v_t}}{e^{-\eta_t \tau} w_t}, \\\
c_t & =\frac{\left(1-\rho_t^2\right) v_t}{e^{-2 \eta_t \tau} w_t^2}+\frac{\left(1-2 e^{-\eta_t \tau} m_t \tau-e^{-\eta_t \tau} w_t \rho_t \sqrt{v_t} \tau\right)^2}{e^{-4 \eta_t \tau} w_t^4 \tau^2}.
\end{aligned}
$$

---

Similar to IV dynamics, the OEV is assumed to have a proportional dynamics under the statistical measure $\mathbb{P}$:

$$
\begin{align}
d V_t(K, T) / V_t(K, T)&=e^{-\eta_t(T-t)}\left(m_t^{\mathbb{P}} d t+w_t d Z_t^{\mathbb{P}}\right),
\end{align}
$$
where $m_t^{\mathbb{P}}$ is a different drift process compared with that under risk-neutral measure $\mathbb Q$, while the instantaneous volatility of volatility $w_t$ are still the same.

The dynamics of the underlying price and return-volatility correlation under the statistical measure $\mathbb{P}$ are assumed to be:

$$
\begin{align}
&d S_t / S_t=\mu_t^{\mathbb{P}} d t+\sqrt{v_t^{\mathbb{P}}} d W_t, 
\\
&\mathbb{E}_t^{\mathbb{P}}\left[d W_t^{\mathbb{P}} d Z_t^{\mathbb{P}}\right]=\rho_t^{\mathbb{P}} d t .
\end{align}
$$

The no-dynamic-arbitrage constraint for OEV can be derived as:
$$
\frac{1}{4} e^{-2 \eta_t \tau} w_t^2 \tau^2 V_t^4
+\left(1-2 e^{-\eta_t \tau} m_t^{\mathbb{P}} \tau-e^{-\eta_t \tau} w_t \rho_t^{\mathbb{P}} \sqrt{v_t^{\mathbb{P}}} \tau\right)V_t^2 
 -\left(v_t^{\mathbb{P}}+2 e^{-\eta_t \tau} w_t \rho_t^{\mathbb{P}} \sqrt{v_t^{\mathbb{P}}} k+e^{-2 \eta_t \tau} w_t^2 k^2\right)  = 0
$$

---

Therefore, if $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ is known, $I_t(K,T)$ and $V_t(K,T)$ can be computed. Conversely, if $I_t(K,T)$ and $V_t(K,T)$is known,$(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ must satisfy the no-dynamic-arbitrage constraint equation. Now, the question are:

1. **How to extract the values of $(m_t,m_t^{\mathbb{P}}, w_t, \eta_t,v_t,v_t^{\mathbb{P}}, \rho_t,\rho_t^{\mathbb{P}})$ from the current shape of volatility surface $I_t(K,T)$ and $V_t(K,T)$?** 
2. **How to estimate the shape of volatility surface $I_{t+1}(K,T)$ and $V_{t+1}(K,T)$ in the next time step, if the current values of $v$ are known?** 

Carr and Wu (2016) uses the Unscented Kalman Filter to solve these 2 problems. In the following section, we will firstly introduce the data used in the empirical analysis, and then show the performance of Unscented Kalman Filter for extracting the near-term dynamics of the volatility.

## 3 Replication: Data

### 3.1 Data description and data preprocessing

To extract the near-term dynamics of the volatility surfaces, this paper uses the dataset of SPX option implied volatility quotes, SPX price time series, interest rate and dividend yield:

#### 3.1.1 Implied volatility quotes on SPX options

The implied volalitity quotes are obtained from IvyDB US by OptionMetrics. The quotes are available from January 8, 1997 to October 29, 2014, 
having $25 \ 054 \ 504$ samples. 

For implied volatility quotes, options with zero volume or open interest are excluded. Meanwhile, options which are not traded in the last three days are removed. All in-the-money options with NaN implied volatility quote are removed.

Carr and Wu (2016) obtain the implied volatility quotes from a major, and their quotes are a matrix on a grid of five fixed relative strikes from 80\% to 120\% of the spot level and eight fixed time to maturities from 1 month to 5 years. To get a similar matrix of dataset, I use cubic spline interpolation to obtain the implied volatility quotes with 5 sets of moneyness ($K/S \in \{0.8, 0.9, 1.0, 1.1, 1.2\}$) and 5 sets of time to maturities ($\tau  \in \{1 \text{ month}, 3 \text{ months}, 6 \text{ months}, 12 \text{ months}, 24 \text{ months}\}$ ). These quotes are sampled weekly every Wednesday, aligned with the setting in Carr and Wu (2016).

For instance, the preprocessed implied volatility quotes on 1997-01-08, which has 25 rows and 4 columns, are shown as follows:


|      | **t**      | **tau (in year)**  | **K/S** | **IV**   |
| ---- | ---------- | -------- | ------- | -------- |
| 1    | 1997-01-08 | 0.083333 | 0.8     | 0.305950 |
| 2    | 1997-01-08 | 0.083333 | 0.9     | 0.245290 |
| …    | …          | …        | …       | …        |
| 25   | 1997-01-08 | 2.000000 | 1.2     | 0.179292 |

#### 3.1.2 SPX daily quotes
The SPX daily quotes are obtained from Bloomberg, available from January 4, 1985 to August 31, 2023, having $10 \ 505 $ samples. Note that SPX daily quotes have 12 years more data than the implied volatility quotes. The reason is that Carr and WU (2016) estimate the historical ORV 10 years earlier than the implied volatility sample from January 8, 1987, which needs the SPX daily time series going back 2 additional years to January 8, 1985, since $\operatorname{ORV}(k,t-\tau,t)$ uses the data from time $t-\tau$ to time $t$, and the longest $\tau$ in my implied volatility quotes is $24 \text{ months}$.

#### 3.1.3 Interest rate
The daily interest rate is proxied by the zero coupon yield in the nominal yield curve of U.S. Treasury, obtained from the official website of the Federal Reserve[^2]. The mnemonic for zero-coupon yield is "SVENYXX".

#### 3.1.4 Dividend yield
The monthly dividend yield computed with Shiller's US Stock market data, obtained from Shiller Data[^3]. 

### 3.2 The average behavior of volatility surfaces and volatility risk premiums

<img src="https://s2.loli.net/2024/12/31/FaBmOKXEfQbh9wq.png" />

### 3.3 The time-series variation of implied and expected volatilities

The time-series trends of at-the-money implied and expected volatilities exhibit similar behaviors. The volatility series experience significant spikes during notable events such as the 1998 Asian financial crisis, the subsequent hedge fund crisis in 1999, and most notably, the global financial crisis in 2008. Another noticeable spike in 2012 aligns with the European sovereign debt crisis. 

However, it is important to note that at-the-money expected volatilities are higher than implied volatilities, which implies that the expected volatilities might be overestimated.

The 90–110\% difference in option implied volatility remains consistently positive throughout the sample period and across all option maturities, indicating a persistently negatively skewed SPX return distribution implied by options. In contrast, the corresponding option expected volatility exhibits significantly less negative skewness and occasionally shifts to positive values.


<img src="https://s2.loli.net/2024/12/31/qG6kIWytXYgCji7.png" />

<img src="https://s2.loli.net/2024/12/31/r64hV5mGY7Molnk.png" />

### 3.4 Volatility of volatility dependence structure

Carr and Wu (2016) assumes that the variance of the volatility changes is proportional to the variance level, which is different from the common setting of the square-root instantaneous variance rate specification.. 

To investigate how the variance of the volatility changes depends on the volatility level, a constant elasticity of variance specification on the implied volatility time series is estimated:

$$
\begin{equation}
    \frac{1}{\Delta t} \operatorname{Var}_t\left(\Delta I_t(k, \tau)\right)=C(\tau)\left(I_t^2(k, \tau)\right)^\beta,
\end{equation}
$$
where $\beta$ would be one under the assumption of Carr and Wu (2016), and zero under the assumption of the square-root instantaneous variance rate. 

To estimate $\beta$, an exponential weighted variance is estimated at first:

$$
\begin{equation}
    E V I_t=\phi E V I_{t-1}+(1-\phi)\left[\left(\Delta I_t\right)^2 / \Delta t\right],
\end{equation}
$$
where $\Delta t=1 / 52$, $\phi=0.97$ \parencite{carrAnalyzingVolatilityRisk2016}. 

Then, a linear regression is used to estimate $\beta$:
$$
\begin{equation}
    \ln E V I_t(k, \tau)=\ln C(\tau)+\beta \ln I_t^2(k, \tau)+e_t .
\end{equation}
$$

<img src="https://s2.loli.net/2024/12/31/KWCyTIJNZ23YQR4.png" />

The table above reports the slope estimates for each implied volatility time series. For all 25 series, the slope estimates are far away from the square-root hypothesis of  $\beta=0$, but close to our proportional specification of  $\beta = 1$, suggesting that the proportional volatility dynamics enjoys better empirical support than the square-root specification.

## 4 Replication:  Extracting state variables from volatility surface

### 4.1 Introduction to the Unscented Kalman Filter

Note that the shapes of the implied volatility surface and the expected volatility surface only depends on the current levels of covariates, and there is no need to specify the full dynamics of covariates. Therefore, the emphasis of the empirical analysis involves the extraction of the states from the two surfaces, without knowing the stat dynamics.

Based on this feature, Carr and Wu (2016) treat the covariates as the hidden variables in a state-space form model and apply the Unscented Kalman Filter to extract the covariates from the volatility surfaces.



Among the eight variables, $(w_t, \eta_t,v_t,v_t^{\mathbb{P}})$ are constrained to be strictly positive, $ (\rho_t,\rho_t^{\mathbb{P}})$ are constrained to be between $(−1, 1)$. For convenience, these variables are transformed so that they can take values on the whole real line:

$$
\begin{equation}
\begin{aligned}
X_t := & {\left[m_t, m_t^{\mathbb{P}}, \ln \left(w_t\right), \ln \left(\eta_t\right), \ln \left(v_t\right), \ln \left(v_t^{\mathbb{P}}\right), \ln \left(\frac{1+\rho_t}{1-\rho_t}\right),\right.} \left.\ln \left(\frac{1+\rho_t^{\mathbb{P}}}{1-\rho_t^{\mathbb{P}}}\right)\right]^{\top} .
\end{aligned}
\end{equation}
$$

The state-transition equation is:

$$
\begin{equation}
X_t=X_{t-1}+\sqrt{\Sigma_x} \varepsilon_t,
\end{equation}
$$
where $\varepsilon_t \in \mathbb{R}^{8 \times 8}$ is normally distributed with zero mean and unit variance, and $\Sigma_x =: Q$ is the covariance matrix. $Q$ is assumed to be a diagonal matrix with distinct diagonal elements.

The measurement equation is:

$$
\begin{equation}
\begin{aligned}
y_t & =h\left(X_t\right)+\sqrt{\Sigma_y} e_t, \\\
h\left(X_t\right) & =
\left[I\left(X_t ; k_1, \tau_1\right), \cdots,I(X_t;k_{25},\tau_{25}),
V\left(X_t ; k_1, \tau_1\right),  \cdots,V(X_t;k_{25},\tau_{25})\right]^{\top},
\end{aligned}
\end{equation}
$$
where $y_t\in \mathbb R^{50\times1}$ denotes the 25 implied volatility quotes and the 25 corresponding OEV estimates on date t, and $h\left(X_t\right)$ denotes the model value of the implied and expected volatility as a function of the states $X_t$, which can be solved from the no-dynamic-arbitrage constraint equations for IV and OEV, respectively. $e _t\in \mathbb{R}^{50\times 50}$ is normally distributed. $\Sigma_y =: R$ is the measurement error covariance matrix, and is i.i.d with error variance $\sigma_I^2$ for the 25 implied volatility and $\sigma_V^2$ for the 25 expected volatility. 

The covariance matrices $Q$ has the structure:

$$
\begin{align}
Q&=\left[\begin{array}{cccc}
\sigma_{1}^2 & 0 & \cdots & 0\\\
0 &\sigma_{2}^2 & \cdots &0 \\\
\vdots & \vdots & \ddots & \vdots \\\
0 & 0 & \cdots & \sigma_{8}^2
\end{array}\right]_{8\times8},
\end{align}
$$

The covariance matrices $R$ has the structure:

$$
\begin{align}
R&=\left[\begin{array}{cccc}
R_I & 0 \\\
0 & R_V \\\
\end{array}\right]_{50\times50},
\end{align}
$$

where 

$$
\begin{align}
R_I&=I_{25} \sigma_I^2, 
\\\
R_V&=I_{25} \sigma_V^2,
\\\I_{25} &=\left[\begin{array}{cccc}
1 & 0 & \cdots & 0\\\
0 &1 & \cdots &0 \\\
\vdots & \vdots & \ddots & \vdots \\\
0 & 0 & \cdots & 1
\end{array}\right]_{25\times25}.
\end{align}
$$

**Here, $(\sigma_{1}^2,\sigma_{2}^2,\sigma_{3}^2,\sigma_{4}^2,\sigma_{5}^2,\sigma_{6}^2,\sigma_{7}^2,\sigma_{8}^2,\sigma_{I}^2,\sigma_{V}^2)$ are estimated by minimizing the sum of squared forecasting errors in a quasi-maximum likelihood setting.**

### 4.2 Pricing performance and state dynamics analysis

In the paper, the first three years volatility quotes are used to estimate 10 covariance parameters. Then, these parameters are applied to predict the volatilities through the whole time slots.  \cref{tbl:pricing_error} reports the average pricing error on each volatility series. The pricing errors are defined as the difference between the observed volatility series and the model-generated values, in volatility percentage points. Given a fixed moneyness, it is shown that the most obvious pricing error occurs at 1-month maturity, which means the model fails to capture the volatility smile at the short maturity. This might be caused by the pure-continuous assumption for the underlying price dynamics \parencite{carrAnalyzingVolatilityRisk2016}. As maturities being longer, the average biases are less severe. 

The pricing errors on the expected volatility surface are much more severe than the implied volatility. This might be caused by the overestimation of the OEV.

<img src="https://s2.loli.net/2024/12/31/PN3Re8LcjJl9GFd.png" />

### 4.3 State dynamic analysis

#### 4.3.1 Instantaneous volatility: $\sqrt{v_t}$, $\sqrt{v_t^{\mathbb{P}}}$

The time-series fluctuations of the two instantaneous volatility series closely align with the variations observed in at-the-money implied and expected volatilities. Meanwhile, the instantaneous volatility series extracted from the expected volatility surface seems to lag behind the solid line extracted from the implied volatility surface, because it is estimated in a backward way.

<img src="https://s2.loli.net/2024/12/31/jC6e8YA5rysNdKn.png" />

#### 4.3.2 Instantaneous drift: $m_t$, $m_t^{\mathbb{P}}$
The instantaneous drift of the implied volatility remains above zero for most of the period, except during the 2002 recession and the 2008 financial crisis. This predominantly positive risk-neutral drift indicates that the at-the-money implied volatility term structure is generally upward sloping. In contrast, the instantaneous drift of is mostly negative, implying that the expected volatility derived from historical sample paths typically exhibits a downward-sloping term structure. The gap between these term structures represents the volatility risk premium, which becomes especially pronounced during the major financial crises of 1998 and 2008.
<img src="https://s2.loli.net/2024/12/31/jCnUTzve7km8QoW.png" />

#### 4.3.3 Instantaneous return-volatility correlation: $\rho_t$,  $\rho_t^\mathbb{P}$
The instantaneous correlation between the SPX index return and the implied volatility stays strongly negative, reflecting the negative skew in the implied volatility surface. By contrast, the instantaneous correlation between the SPX index return and the expected volatility flucuates more, somtimes being positive, meaning that the expected volatility surface is not always negatively skewed.

<img src="https://s2.loli.net/2024/12/31/y2YBZGMEsDvH5af.png" />


#### 4.3.4 Volatility of volatility: $\omega_t$
The volvol estimated in Carr and Wu (2016) tends to be high when the volatility levels are high, while the volvol estimates here seem to be irregular. This might be caused by the overestimated OEV. Since the OEV does not match the observed data, the volvol coefficient will not accurately describe the volatility of volatility.

<img src="https://s2.loli.net/2024/12/31/JtUglL7QPMq6kZW.png" />

#### 4.3.5 Maturity decay: $\eta$
The extracted series remain stable except during the 1998 and 2002 recessions, when the estimates rise significantly. This recession stands out with unique characteristics: short-term volatility is elevated, the term structures for both implied and expected volatilities slope downward, and short-term volatility exhibits much greater variation compared to long-term volatilities. Although short-term volatility is high during both the financial crises and this recession, long-term implied volatilities increase less during the recession, indicating that investors were notably less concerned about the recession compared to the financial crises.

<img src="https://s2.loli.net/2024/12/31/uPqM9AEcd25iebx.png" />

## （5 遇到的问题和解决方法）
Q1: 使用 cubic spline interpolation 的 iv 曲面可能不满足无套利条件。

A1: 或许可以尝试[这篇文章](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1964634#paper-citations-widget)里的插值方法。

---

Q2: 在 quasi-maximum-likelihood 的设定下以最小化 pricing error 的 SSE 为损失函数估计 Q 和 R 时，最终的估计值对初始值很敏感。可能的原因是，minimizing pricing error SSE 是 non-convex 的，因此估计值是局部最优解。

A2: 尚未解决。

---

Q3: 如果过程噪音矩阵 Q 的值太小，那么状态协方差矩阵 P 迭代时不一定是半正定的。

A3: 在每次更新完 P 之后，进行下述操作：

1. $P^{\prime}:=\frac{P+P^T}{2}$
2. 对 $P'$ 做特征分解：$P^{\prime}=V D V^T$，其中 $V$ 是特征向量，$D$ 是主对角线元素为特征值的对角矩阵
3. 将 $D$ 主对角线上的元素替换为 $\max \left(D_{i j}, 0\right)+\epsilon$，其中 $\epsilon$ 是一个较小的正数（我取了1e-6）。将新的对角矩阵记作 $D'$
4. 获取新的$P^* := V D^{\prime} V^T$



[^-1]: VGVV 是 Vega-Gamma-Vanna-Volga 的缩写。
[^-11]: 注意这里是 variance 而非 volatility。
[^0]: Option Expected Volatility is a new concept proposed in Carr and Wu (2016). The definition of OEV can be seen [in this subsection](#definition-of-orv-and-oev).
[^1]: It is important to note that the PDE is derived for the relation which the stochastic terms must satisfy to avoid dynamic arbitrage. In other words, **this PDE is not used for solving the value function $B(S_t, I_t, t)$ (since it is already given),** and the coefficients in the SDE are not deterministic as well.
[^2]: Source: [https://www.federalreserve.gov/data/nominal-yield-curve.htm](https://www.federalreserve.gov/data/nominal-yield-curve.htm).

[^3]: Source: [https://shillerdata.com/](https://shillerdata.com/). The dividend yield is computed by dividing the dividend by the price of stock in the file "ie_data (xls)".



[^ukf]: 个人感觉这篇讲解卡尔曼滤波的教程很易读： https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python。

[^carr-and-wu]: Peter Carr, Liuren Wu,
Analyzing volatility risk and risk premium in option contracts: A new theory,
Journal of Financial Economics,
Volume 120, Issue 1,
2016,
Pages 1-20,
ISSN 0304-405X,
https://doi.org/10.1016/j.jfineco.2016.01.004.
(https://www.sciencedirect.com/science/article/pii/S0304405X16000052)
