---
title: 为啥还有人直接用 LSTM 预测股价嘞？
description: 小小总结了使用 LSTM 预测股价时需要注意的事项。
toc: true
tags: 
- 金融计量
categories:
- 回忆
series:
date: '2022-06-05T02:34:12+08:00'
featuredImage:
draft: false
---

最近提交了论文的最终稿，闲来无事和其他同学交流了一下，发现有俩同学在用 LSTM 预测股价。简单扫了一遍他们的文章，发现他在数据的处理上比较粗糙，没有太大实用价值。支撑我作出这个判断的，是他们没做到以下几件事：

1. 在数据处理上，股价数据属于时序数据，需要先做平稳性检验，对输入数据做对数差分，保证预测变量和特征变量都是平稳的。

2. 在模型选择上，由于金融时序数据的信噪比比较低，所以模型的参数不能太多，以免过拟合。

3. 在预测目标上，选择了股价的绝对值（而非收益率），而股价这玩意是不稳定序列，任何扰动都是永续的，因此方差随时间会趋向无穷大。同时，从实际应用的角度上看，我们其实更希望得到的是下一期的股价和这一期相比，是涨了还是跌了，而不是预测股票价格的绝对值。

    

如果中了以上几条，相当于训练模型前啥准备工作都没做。这会导致什么结果呢？根据笔者以前的试验，LSTM 大概率只能学到一堆噪声（$y_t^'=y_{t-1}+\epsilon^'$），甚至直接把前一期的收盘价作为当期的预测值输出——在这种情况下，假设单日内股价涨跌幅限制 10%（比如 A 股市场），那么在使用 MSE 的情况下，预测的误差也一定不会超过 10%。虽然预测误差看起来不大，但实际上模型没学到啥有用的东西，没法用于在股市里的获利。

举个例子，某只股票今天的价格是10元，用模型预测其明天的价格是10.25元，结果真的到了第二天，发现股票价格跌到了 9.75 元。这时候，你计算绝对股价的误差，可能会发现，$(10.25-9.75)/9.75 = 5.13\%$，好像预测差距并不大，但问题就是，股票每天的波动本来就不大。如果你真拿这个模型去股市里实战，你可能会发现，这个模型虽然可以做到每天预测股价的绝对值的误差都都很小，但是每天的涨跌其实都是乱猜的，怕不是没多久就赔麻了。