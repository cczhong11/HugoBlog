---
title: "Cross-validation"
date: "2018-05-01"
description: "Basic idea for Cross-validation"
categories:
    - "Machine learning"
---

# Cross-validation
## Hold - out validation

In this method, we will have 70% training data and 30% validation data. When we do the training, we will only use the training data, then we will use the validation data to test our model and tuning the **hyperparameters**

- parameters:$\theta$
- hypermeters: degree of polynominal, amount of regulazation...

## K-hold validation

In this method, we will divide the data into k part, then we will use k-1 data to train the model and have the rest one validate the model.

## Leave-one-out validation

In this method, we will only leave one data point out the training and do the similar with k-fold validation.

The problem for above methods is it does not show advantages over normal crossvalidation and it is okay for us to use the normal one. By the way, we could not use these two over time series data, because we could not depend the previous data on the latter data point. 

# Another problem

Using normal cross-validation could overfit the hyperparameters, therefore we could split the original dataset to 3 part: training, validation and testing. But when we use the testing data once, it is possible that it is not so true. The best solution is to evaluate your system in wild.