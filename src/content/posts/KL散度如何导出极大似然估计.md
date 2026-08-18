---
title: KL散度如何导出极大似然估计
description: An interesting equivalence
pubDatetime: 2026-08-18T15:52:21+08:00
---

MLE（Maximum Likelihood Estimation）是我们建模概率分布的常用方法。它的基本思想很简单：

> [!Important]
> 给定一组数据，找一个分布，使得在此分布下，该组数据出现的概率（称为“似然”）最大。

本文将用一些简短的推导说明，KL散度的最小化将同样给出MLE. 
KL散度衡量两个概率分布之间的“距离”，因此，用KL散度刻画分布建模的质量是自然的。

设$X_1, X_2, ..., X_n$ 是分布$P^*$ 给出的一组观测数据。考虑分布$P$与$P^*$的KL散度：
$$
\begin{aligned}
KL(P^*||P)&=\sum_xP^*(x)\log\frac{P^*(x)}{P(x)} \\
&=\sum_xP^*(x)\log P^*(x)-\sum_xP^*(x)\log P(x) \\
&=\mathbb{E}_{P^*}[\log P^*(x)]-\mathbb{E}_{P^*}[\log P(x)]
\end{aligned}
$$
我们想要找一个$P$, 使得上式最小。注意上式第一项由真实分布$P^*$决定，与优化目标无关。因此最小化KL散度等价于最大化

$$
\mathbb{E}_{P^*}[\log P(x)]
$$

注意$X_1,...,X_n$由真实分布$P^*$给出，根据大数定律，上面的期望可以由

$$
\frac{1}{n}\sum_{i=1}^n\log P(X_i)=\frac{1}{n}\log\prod_{i=1}^nP(X_i)
$$

给出。
其中

$$
\log\prod_{i=1}^n P(X_i)
$$

正是观测数据在概率模型$P$下的对数似然。


