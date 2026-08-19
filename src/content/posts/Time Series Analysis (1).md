---
title: Time Series Analysis (1)
description: 时间序列分析
pubDatetime: 2026-08-19T23:58:02+08:00
tags:
  - maths
---
时间序列分析的目标，是理解序列中前面的变量如何影响后面的变量，并据此对变量未来的取值进行预测。形式化地说，时间序列处理的对象是$\{X_t\}_{t=0}^\infty$, 其中，$X_i$为随机变量, $i\in \mathbb{N}$. 

既然是随机变量，我们自然关心每个$X_t$的均值和方差。此外，为了刻画时间上的“前面影响后面”，我们还关心$X_i, X_j,i\ne j$之间的协方差。为此，我们引入几个函数：
* 期望函数$\mu_{Xt}:=\mathbb{E}[X_t]$;
* 自协方差函数(autocovarience function)$\gamma_X(s,t):=\text{cov}(X_s,X_t)$;

最简单的随机变量是**白噪声**。 要求$\mathbb{E}[X_t]=0$, 且任意的$X_i, X_j, i\ne j$都彼此独立。

时间序列中的一个重要性质是**stationarity**. 我们这里讨论**weak stationarity**，并将其称为stationarity. （大约中文译为稳定性，我没有确认过） 
对时间序列$\{X_t\}_{t=0}^\infty$，若有
* $\mu(t)=\mu,\forall t$
* 若$j-i=s-t$, 则$\gamma(i,j)=\gamma(s,t)$
则称时间序列是stationary的. 直觉上来说，就是：
* 整个时间序列都在同一水平线上波动；
* 时刻间的linear corelation仅仅与时刻时间的距离有关。

对于stationary time series, 由于autocovarience function的取值仅仅与两个时刻的距离有关，因此可以简单地记为
$$
\gamma(h):=\gamma(t,t+h)
$$
