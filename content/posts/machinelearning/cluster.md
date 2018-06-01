---
title: "Clustering"
date: "2018-05-01"
description: "This blog tells you do cluster"
categories:
    - "Machine learning"
---


# unsupervised learning 
the general philosophy of unsupervised learning is that we want to discover some kind of structure in the data. Different unsupervised learning methods work in very different ways, and discover very different kinds of structure, but they all have this similar element.

Recall from our presentations on supervised learnign that the three aspects of a supervised learning algorithm are: 
1. a hypothesis function;($R^n -> R^n$). Mapping input back to this input **space.The goal of this hypothesis class is to approximately reconstruct the input**
2. a loss function; the goal of an unsupervised learning loss function is to measure the **difference between the hypothesis function and the input itself**.
3. a method for minimizing the average loss over the training data. in the unsupervised setting there (just empirically) is a bit more **variation** in the methods people use to solve this optimization problem. This is because the hypothesis functions themselves often involve discrete terms or other similar elements that cannot be differentiated (we’ll see this is the case of k-means clustering), or because particular optimization methods can provide exact solution 

# kmeans
## Hypothesis function
The parameters $\theta$ of our hypothesis function just include the centers themselves. 
$$\theta = \{\mu^{(1)}, \ldots, \mu^{(k)}\}$$

The function is $$h_\theta(x) = argmin_{\mu \in \{\mu^{(1)}, \ldots, \mu^{(k)}\} } \|\mu - x\|_2^2$$
## Loss function
The loss function used by k-means is simply the squared loss
$$\ell(h_\theta(x), x) = \min_{\mu \in \{\mu^{(1)}, \ldots, \mu^{(k)}\}} \|\mu - x\|_2^2$$

## Optimization 
Finally, let's now consider the ML optimization problem that results from the hypothesis and loss above,
$$minimize_\theta \;\; \frac{1}{m} \sum_{i=1}^m \min_{\mu \in \{\mu^{(1)}, \ldots, \mu^{(k)}\}} \|\mu - x^{(i)}\|_2^2.$$

1. choose centre randomly
2. repeat until convergence
3. for every i, set $c^{(i)}=argmin ||x^{(i)}-\mu_j||^2$ $c^{(i)}$ is label for each point
4. for every $\mu_j$ set $\mu_j=\frac{\sum (c(i)=j)x^{i}}{\sum (c(i)=j)}$ Each centroid is the geometric mean of the points that have that centroid's label. Important: If a centroid is empty 

```python
def kmeans(X, k, max_iter=10, rand_seed=0):
    np.random.seed(rand_seed)
    Mu = X[np.random.choice(X.shape[0],k),:]
    for i in range(max_iter):
        D = -2*X@Mu.T + (X**2).sum(axis=1)[:,None] + (Mu**2).sum(axis=1)
        y = np.argmin(D,axis=1)
        Mu = np.array([np.mean(X[y==i],axis=0) for i in range(k)])
    loss = np.linalg.norm(X - Mu[np.argmin(D,axis=1),:])**2/X.shape[0]
    return Mu, y, loss
```

The problem for k-means is it is possible to get into local optima. 

# Expectation Maximization

the EM algorithm gives an efficient method for maximum likelihood estimation. Maximizing ℓ(θ) explicitly might be difficult, and our strategy will be to instead repeatedly construct a lower-bound on l(E-step), and then optimize that lower-bound (M-step).

Lets review EM. In EM, you randomly initialize your model parameters, then you alternate between (E) assigning values to hidden variables, based on parameters and (M) computing parameters based on fully observed data.

**E-Step**: Coming up with values to hidden variables, based on parameters. If you work out the math of chosing the best values for the class variable based on the features of a given piece of data in your data set, it comes out to "**for each data-point, chose the centroid that it is closest to, by euclidean distance, and assign that centroid's label**." The proof of this is within your grasp!

**M-Step**: Coming up with parameters, based on full assignments. If you work out the math of chosing the best parameter values based on the features of a given piece of data in your data set, it comes out to "**take the mean of all the data-points that were labeled as c**."

# how to choose k

the loss should continue to decrease for larger numbers of centers (the more centers, the closer any given point will be to them). But unlike supervised learning, there is not even a good analogue of cross-validation that we can use here: this property of lower loss will typically also apply to a validation set as well.

A common strategy is rather just to plot the loss versus the number of clusters and try to find a point that is “good enough” in terms of loss versus the number of cluster (i.e., where adding additional clusters doesn’t help much).

it is very difficult to infer anything about the “real” number of clusters in the data from running k-means (in fact, you should really never try to do this)

- Heuristic: Find large gap between k -1-means cost and kmeans cost.
- Hold-out validation/cross-validation on auxiliary task (e.g.,supervised learning task).
- Try hierarchical clustering