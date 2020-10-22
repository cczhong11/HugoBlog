---
title: "Probability"
date: "2018-05-01"
description: "Basic idea for Probability"
categories:
    - "Machine learning"
---

# Probability

Here we want to talk about some basic knowledges of probability. In the beginning, we need to understand some notations in probability and they are confused in some times.

$p(x),p_x$ here, $x$ is an exact number and we could have an exact number for this value from 0 to 1. For exmaple, $p(1)=0.5,p(0)=0.5$

$p(X)$ here, $X$ is a random variable, it represents all the possible value. We could use $p(X=1)$ to represent the exact value. $p(X)$ is a distribution for $X$

$p(X;\theta)$ means there is a parameter $\theta$ could control the distribution $X$

There are also two important measurements for any distribution of data.

## Expection

$\mathbf{E}[X] = \sum_x x \cdot p(x).$ The expectation of a random variable is defined as the product between the value the random variable takes on and its probability, summed over all the values that the variable. One of the most important properties of expectations is the **linearity**. We could say $\mathbf{E}[X_1 + X_2] = \mathbf{E}[X_1] + \mathbf{E}[X_2]$. Additional, the expectation of a product of random variable is equal to the expectation of the product, $\mathbf{E}[X_1X_2] = \mathbf{E}[X_1]\mathbf{E}[X_2]$

## Variance

It is defined as $\mathbf{Var}[X] = \mathbf{E}[(X - \mathbf{E}[X])^2]$. 
$$ \begin{aligned}
\mathbf{Var}[X] & = \mathbf{E}[(X - \mathbf{E}[X])^2] \\
& = \mathbf{E}[X^2 - 2 X \mathbf{E}[X] + \mathbf{E}[X]^2]  \\
& = \mathbf{E}[X^2] - 2\mathbf{E}[X \mathbf{E}[X]] + \mathbf{E}[\mathbf{E}[X]^2] \\
& = \mathbf{E}[X^2] - 2\mathbf{E}[X] \mathbf{E}[X] + \mathbf{E}[X]^2 \\
& = \mathbf{E}[X^2] - \mathbf{E}[X]^2
\end{aligned}
$$

## common distribution
### Bernoulli distribution

A simple distribution over binary {0,1} random variables

$p(X=1;\phi)=\phi$, $p(X=0;\phi)=1-\phi$

### Categorical distribution

It is mulitple categories version for Bernoulli distribution.

$p(X=i;\phi) = \phi_i$

### Geometric distribution

The geometric distribution is an distribution over the positive integers, can be viewed as the number of Bernoulli trials needed before we get a “1”

$p(X=i;\phi)=(1-\phi)^{i-1}\phi$

### Poisson distribution

Distribution over non-negative integers, popular for **modeling number of times an event occurs within some interval**

$p(X=i;\lambda) = \frac{\lambda^ie^{-\lambda}}{i!}$

### Gaussian distribution

It is the most important distribution in the world. 

$P(x) = \frac{1}{{\sigma \sqrt {2\pi } }}e^{ - ( {x - \mu } )^2/{2\sigma ^2 } }$

### Multivariate Gaussians


$f_Y(x)=\frac{1}{\sqrt{(2\pi)^n|\boldsymbol\Sigma|}}
\exp\left(-\frac{1}{2}({x}-{m})^T{\boldsymbol\Sigma}^{-1}({x}-{m})
\right)$

Here, $\Sigma$ is covariance matrix.

