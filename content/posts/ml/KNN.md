---
title: "K Nearest Neighbor, K-Means and EM algorithm"
date: "2018-12-01"
description: "An article about some basic concepts for KNN"
categories:
    - "ML"
---

# K Nearest Neighbor, K-Means and EM algorithm

These three algorithms are used for clustering. Let's talk about K Nearest Neighbor(KNN) first. 

## K Nearest Neighbor

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/KnnClassification.svg/320px-KnnClassification.svg.png?1546962079122)

Example of k-NN classification. The test sample (green circle) should be classified either to the first class of blue squares or to the second class of red triangles. If k = 3 (solid line circle) it is assigned to the second class because there are 2 triangles and only 1 square inside the inner circle. If k = 5 (dashed line circle) it is assigned to the first class (3 squares vs. 2 triangles inside the outer circle).




In edger learning, it will choose a hypothesis space H, and it is the hard bias. Then given the training data, it can find a member of H, h, that best fits in it. Then h could be applied to any data.

The lazy learning is doing nothing in training step, just saving all data. In test step, it will make a prediction based on data in this step.

For K-nearest Neighbors, it finds K nearest neighbors in the training data. Then average the label or find the most labels to predict this data's label. If K=1, it always finds itself. There is no hard bias for KNN, and the soft bias is nearby point should have the same label. 

The problem for KNN is: it is possible that different dimension will have different meaning, it is not wise just treat them as same. We could use $w _ i$ to deal with different dimension problem.$f(q) = \frac{\sum K(dist(x _ i,q))y _ i}{\sum K(dist(x _ i,q))}$

The kernel function is any positive semidefinite function of any two inputs. We could use the kernel to estimate small dataset and aggregate them to estimate the whole dataset. We can also use kernel smoothing. 

## K-Means

In the beginning, pre-define K centers in the observation. 
 
It contains two steps, and the first one is to assign the center of the cluster. Assign each observation to the cluster whose mean has the least squared Euclidean distance. The second one is to update the new center which is the mean of the observations in the new cluster. Iterate many times, until the centers will not change.

The algorithm does not guarantee convergence to the global optimum. The result may depend on the initial clusters. As the algorithm is usually fast, it is common to run it multiple times with different starting conditions.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/K-means _ convergence.gif/440px-K-means _ convergence.gif)

## EM algorithm

The EM algorithm is a general idea to calculate the unknown parameters. Let's use Gaussian Mixture Models as an example to talk about this model. 

If there are many gaussian models mixing, we could get sampled data from this model. How could we estimate the $\mu _ i$ for these value?

Follow the MLE idea, we could get likelihood function with $\prod _ {i} e^{-\frac{(x _ {i}-\mu _ {j})^{2}}{2\sigma^{2}}}$ with $\mu _ j$. If we consider the prior $\lambda _ j$ for model j. The most possible for $\mu$ is 
$$\boldsymbol{\mu} _ {ML}=\arg\max _ {\mu _ {1},\mu _ {2}}\prod _ {i}\sum _ {j}\lambda _ {j}e^{-\frac{(x _ {i}-\mu _ {j})^{2}}{2\sigma^{2}}}$$. But there is no analytical solution for this function, EM algorithm could give a local maximum, not necessary to be global maximum.

If we could get a table Z, when $x _ i$ from Gaussian j,$Z _ {i,j}=1$,otherwise 0. The $\hat \mu _ j = \sum _ i Z _ {i,j}X _ i / \sum Z _ {i,j}$. If we don't know it, we could estimate it with $\hat \mu _ j = \sum E[Z _ {i,j}|\bar \mu] X _ i / \sum E[Z _ {i,j}|\bar \mu]$.

The basic idea for EM is: 

1. Assume the $\mu _ j$ in the model
2. Classify data point to each model
3. Re-estimate the $\mu _ j$
4. Repeat

In GMM:

1. E steps: $$E \left[z _ {i,j}|\mu^{[k]}\right]=\frac{\lambda _ {j}L\left(x _ {i}|\mu=\mu _ {j}^{[k]}\right)}{\sum _ {j^{\prime}}\lambda _ {j^{\prime}}L\left(x _ {i}|\mu=\mu _ {j^{\prime}}^{[k]}\right)}$$
     In this step, it could classify data point to each cluster.
2. M step: $$\mu _ {j}^{[k+1]}=\frac{\sum _ {i}E\left[z _ {i,j}|\mu^{[k]}\right]\cdot x _ {i}}{\sum _ {i}E\left[z _ {i,j}|\mu^{[k]}\right]}$$ In this step, it could re-estimate the $\mu _ j$

if L is bounded, then it can converge to a (not necessarily global) local minimum.

We could see the KMeans algorithm has the similar idea with EM algorithm.