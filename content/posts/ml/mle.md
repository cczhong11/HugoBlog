---
title: "Maximum Likelihood Estimation basic concepts"
date: "2018-12-03"
description: "An article about some basic concepts for MLE"
categories:
    - "ML"
---


# Maximum Likelihood Estimation

This is a fundamental algorithm, but I still want to write a blog about it. Let's start with the basic idea.

## Probability

If we have a situation $S_1$ and event A may happen. For example, under the same situation, the event A happens 7 out of 10. We could see the frequency of event A is 0.7. Under much experiment with the same situation, if we still get the same result, we could see the probability of event A in this situation is 0.7.

Another important concept is the conditional probability. If we have another event B, and B may happen when event A happened. For instance, under the same situation, event B happens 8 out of 10 when event A happened. The frequency of event B when event A happen is 0.8. If event B had the same pattern all the time, we could see the conditional probability of event B when event A happened is $P(B|A)=0.8$, the probability of event A and event B happened at the same time is $P(A, B)$. 

Independence is also important to know here. If event A and event B are independent with each other, which means, event A happened or not had nothing to do with the event B, we could get the P(A，B) = P(A)*P(B).

## Bayes' theorem 

It is the most important theorem in the statistics world. 

$$P ( A | B ) = \frac { P ( B | A ) P ( A ) } { P ( B ) }$$

If we want to know the probability of the A conditional on B, we can use the probability of the B conditional on A and probability of A and B to know. In the real world, the probability of the A conditional on B is often hard to get, and it can tell us the probability of a reason given situation. We can quickly get the probability of a situation given the reason. The situation A is mostly data. The reason B is mostly hypothesis.

Prior is an important concept in Bayes' thinking. It captures the belief for the right answer before seeing the data. In uniform distribution, prior is $\pi(h) = 1/|H|π(h)=1/∣H∣$ if h in H. Here $P(A)$ can be prior

The posterior is the probability of reason after seeing the data. It is $p(A|B)$.

The likelihood is another important concept. It will tell you under the given the parameters, what is the probability for data, expressing as $L(B|A)$.

So in Bayes' theorem, we can express it as prior * likelihood/evidence. Evidence is $P(B)$. 

## Maximum A Posteriori and Maximum Likelihood Estimation

From the Bayes rule, we know $$P(\theta|X)=\frac{P(X|\theta)P(\theta)}{P(X)}$$. 

Because $P(X)$ is the same, we could omit this value. $P(\theta|X) \propto P(X|\theta)P(\theta)$.

If we want to find the most likely $\theta$, we need to have $\theta_{ MAP }=\underset{\theta} {\arg\max} P(X|\theta)   P(\theta)$ .

If data point is independent, we can use $\underset{\theta}{\arg\max}\log \prod _ { i } P (x _ {i} |\theta)P(\theta)$ to calculate the result. To make it easier to calculate, we use log in most cases. It is called as Maximum A Posteriori.

For Maximum Likelihood Estimation, we want to maximize the likelihood function, which is $P(X|\theta)$. We want to know which theta could result in the given data.

$$
\begin{aligned}\theta _ {MLE} &= \underset{\theta}{\arg\max}P(X|\theta)=\underset{\theta}{\arg\max}\prod _ {i}P\left(x _ {i}|\theta\right)\end{aligned}
$$

The difference between MLE and MAP is $P(\theta)$, if all possible reasons we need to consider is the same, MLE is equal to MAP.

## Naive Bayes


Naive Bayes is the extension for MAP algorithm. We could use $$j_{NB} = argmax_j p(cat=j)\prod p(a_i|j)$$ to calculate the most possible labels for the given data.

In the text field, we could assume the probability for the given label is based on the frequency of labels in the whole dataset. Then using the frequency of $a_i$ in the given label to calculate $P(a_i|j)$

Sometimes, if there is no data under some labels in the training data, we need to use Laplace smoothing, adding 1 to both fraction side. $$P(w|c)=\frac{\operatorname{count}(w,c)+1}{\operatorname{count}(c)+|V|}$$


