---
title: "Maximum likelihood estimation"
date: "2018-05-01"
description: "Basic idea for Maximum likelihood estimation"
categories:
    - "Machine learning"
---

# Maximum likelihood estimation

In the beginning, we could get a sample set $x_1,x_2,...$, and we assume these points are independent with each other. Then we could get the possibility for us to get these points.
$p(x_1,x_2,x_3...,x_m;\theta) = \prod_{i=1}^m p(x_i;\theta)$

So, what's the most possible $\theta$ under this circumstance, it is when this possibility get its maximum value. Therefore, we want to maximize $\theta$, to calculate 
$$maximize_\theta \; \prod_{i=1}^m p(x^{(i)};\theta)$$
If we take the log to $p(x)$, we could get the **log likelihood** of the data, which is also equivalent to that value.
$$maximize_\theta \; \frac{1}{m} \sum_{i=1}^m \log p(x^{(i)};\theta)$$

Therefore, when we need to calculate $\phi$ in Bernoulli distribution, we could use $p(X = x; \phi) = \phi^x (1-\phi)^{(1-x)}$ to capture $x=1$ and $x=0$ at the same time. Then we could rewrite it to $maximize_{\phi} \sum_{i=1}^m (x^{(i)}\log\phi + (1-x^{(i)}) \log (1-\phi))$

In order to maximize this equation, let's take the derivative and set it equal to 0. So, we could know why we could estimate $\phi$ just like this.
$$\begin{split}
\frac{d}{d \phi} \sum_{i=1}^m \left (x^{(i)}\log\phi + (1-x^{(i)}) \log (1-\phi) \right ) 
& = \sum_{i=1}^m \frac{d}{d \phi} \left ( x^{(i)}\log\phi + (1-x^{(i)}) \log (1-\phi) \right )  \\
& = \sum_{i=1}^m \left ( \frac{x^{(i)}}{\phi} - \frac{1-x^{(i)}}{1-\phi} \right )
\end{split}$$

$$\begin{split}
& \sum_{i=1}^m \left ( \frac{x^{(i)}}{\phi} - \frac{1-x^{(i)}}{1-\phi} \right ) = 0 \\
\Longrightarrow \;\; & \frac{\sum_{i=1}^m x^{(i)}}{\phi} - \frac{\sum_{i=1}^m (1-x^{(i)})} {1-\phi} = 0 \\
\Longrightarrow \;\; & (1-\phi) \sum_{i=1}^m x^{(i)}  - \phi \sum_{i=1}^m (1-x^{(i)}) = 0 \\
\Longrightarrow \;\; & \sum_{i=1}^m x^{(i)} = \phi \sum_{i=1}^m (x^{(i)} + (1-x^{(i)})) \\
\Longrightarrow \;\; & \sum_{i=1}^m x^{(i)} = \phi m \\
\Longrightarrow \;\; & \phi = \frac{\sum_{i=1}^m x^{(i)}}{m}.
\end{split}$$


## Naive bayes
Naive bayes is an easy method, the key is to calculate $$p(Y \mid X) = \frac{p(X \mid Y)p(Y)}{\sum_y p(X \mid y) p(y)}$$. We could calculate $P(X \ mid Y)$ and $P(Y)$ respectively. 

The naive Bayes approach, however, is to make the additional assumption that the individual features  $X_i$  are conditionally independent given  $Y$ . This lets us represent the condition $p(X \mid Y) = \prod_{i=1}^n p(X_i \mid Y)$. 

For example, we need to detect spam mail. We need to training the classifier. The training data tells us $P(words|spam)$ and $P(words'|not spam)$, then we could get the $P(spam)$, we could use $P(spam|words'') = \frac{p(words|spam)P(spam)}{p(words|spam)P(spam)+p(words|not spam)P(not spam)}$

We could use log to avoid too small value yield from $\prod$, like $\log p(y) + \sum_{i=1}^n \log p(x_i \mid y)$. 

We also could use laplace smoothing to avoid zero. $\phi_i^y = \frac{\sum_{j=1}^m x_i^{(j)} \mathrm{1}\{y^{(j)} = y\} + 1}{\sum_{j=1}^m \mathrm{1}\{y^{(j)} = y\} + 2}$

## logistic regression

We could get this formula from logistic regression and we also could think it using MLE.

$minimize_\theta \sum_{i=1}^m \ell_{\mathrm{logistic}}(h_\theta(x^{(i)}),y^{(i)}) \; \equiv \; minimize_\theta \;\; \sum_{i=1}^m \log(1+\exp(-h_\theta(x^{(i)})\cdot y^{(i)})$



