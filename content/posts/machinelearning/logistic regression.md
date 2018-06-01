---
title: "Logistic regression"
date: "2018-05-01"
description: "Basic idea for Logistic regression"
categories:
    - "Machine learning"
---

# Logistic regression 

This method is a varanice of a linear classier. The key this is we want to use $sigmod(\theta X)$ to classifer value.

## Gradient descent

This method like linear regression, it needs Least square minimization for error. Because there is only 0 and 1. Therefore we could use probability to identify the error. $g(z) = \frac{1}{1+e^{-z}}$

$P(y=0|x,\theta) = sigmod(\theta X)$

$P(y=1|x,\theta) = 1-sigmod(\theta X)$

这里使用最大似然估计，目的是得到在已知样本的情况下最有可能的$\theta$ 的值。

$L(\theta) = \prod p(y^i|x^i;\theta)$ 

然后我们对其log求导，每次下降为 $(y^i - g(\theta x^i))x_j^i$

所以只需要每次将参数进行下降即可

## Persudo code

Pseudocode: given α, ${(x^i, y^i)}$

- Initialize $\theta = <1, .., 1>^T$

- Perform **feature scaling** on the examples’ attributes
- Repeat until convergence
- for each j = 0, .., n:
    - $\theta_j' = \theta - \alpha \sum_i (y^i-h_a(X^i))x_j^i$
- for each j = 0, .., n:
    - $a_j = a_j'$
