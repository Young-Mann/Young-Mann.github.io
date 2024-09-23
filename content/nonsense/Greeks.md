---
title: "IV 或 T 的变化对 Greeks 有何影响"
date: 2023-11-04T20:42:42+01:00
tags:
- 期权定价
toc: true
draft: false
---

今天在看希腊字母这部分的内容，画了 IV 或 T 变化时的 Greeks，以帮助自己记忆。图中的初始参数设定为：K=200，r=0.04，T=1，IV=0.2。IV 变化时的可能取值：{0.1, 0.3, 0.5, 0.7, 0.9}；T 变化时的可能取值为：{0.25, 1, 3, 5, 10} (以年为单位)。

## 总览

{{< figure
  src="https://s2.loli.net/2023/11/05/sp3xhU8eVSyqX7K.png"
  class="larger"
  caption="K=200, r=0.04, T=1, IV=0.2"

>}}



接下来以 Call Option 为例，分析 T 或 IV 变化时，期权的 Greeks 将如何变化。



## Delta

### IV 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/p2bjXCZE7M4BKdk.png"
  class="larger"
  caption="不同 IV 下的 Delta"

>}}

从上图可知，随着 IV 越来越小，当 $S_t$ 接近 $K$ 的水平时，Delta 的变化得更剧烈，这一部分的曲线也更加陡峭。相应的，两侧的曲线则更加平缓。

### T 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/alMRBfDuzg3Or5y.png"
  class="larger"
  caption="不同 T 下的 Delta"

>}}

T 变化的影响在方向上与 IV 变化的影响相同。随着 T 越来越小时， $S_t$ 越接近 $K$ ，Delta 就变化得更剧烈。



## Gamma

### IV 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/TuWYrL5kJesM6Uq.png"
  class="larger"
  caption="不同 IV 下的 Gamma"

>}}

IV 越大，Gamma 变化越平缓。

### T 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/u8GwMnYiXQIsbkV.png"
  class="larger"
  caption="不同 T 下的 Gamma"

>}}

T 越大，Gamma 变化越平缓。

## Vega

### IV 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/Spw2WkUX4g6iKYB.png"
  class="larger"
  caption="不同 IV 下的 Vega"

>}}

IV 越大，Vega 变化越平缓。

### T 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/6aOWbyt7fxIHroi.png"
  class="larger"
  caption="不同 T 下的 Vega"

>}}

T 越大，Vega 变化越平缓。

## Theta

这里 IV 和 T 变化带来影响的方向不同。

### IV 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/twNLJDfhZ1SPYam.png"
  class="larger"
  caption="不同 IV 下的 Theta"

>}}

IV 越大，Theta 波动越剧烈。

### T 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/CoAB69hKvRTVmdr.png"
  class="larger"
  caption="不同 T 下的 Theta"

>}}

T 越大，Theta 波动更平缓。



## Rho



### IV 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/MDNyGR9S1aBTXZK.png"
  class="larger"
  caption="不同 IV 下的 Rho"

>}}

Rho 受到 IV 变化的影响和 Delta 类似。IV 越小，Rho 波动越剧烈。

### T 变化的影响

{{< figure
  src="https://s2.loli.net/2023/11/05/JTRPHbFVfAdvWcO.png"
  class="larger"
  caption="不同 T 下的 Rho"

>}}

T 越大，Rho 的变化程度也越大。



