---
layout: post
title:  "Deep Learning Notes"
date:   2020-02-14 16:00:00 +0530
categories: Deep Learning
comments: true
---

## Deep Learning Notes

**Work in progress**

Notes from what I've learned in my DL Course - CSE 641

## RCNN

<https://www.analyticsvidhya.com/blog/2018/10/a-step-by-step-introduction-to-the-basic-object-detection-algorithms-part-1/>  

<https://lilianweng.github.io/lil-log/2017/12/31/object-recognition-for-dummies-part-3.html>  


## Practical Aspects of DL

### Gradient Descent Blog [[link]](https://towardsdatascience.com/10-gradient-descent-optimisation-algorithms-86989510b5e9)

Exponential moving average
    - Past gradient values are given higher weight than current
    - Ensures current gradient does not change the value a lot

### Terms

- fan in: max number of inputs that a system can accept
- fan out: max number of outputs system can afford

**PCA vs KLT**
This is well known. Recall that PCA is a spectral decomposition of the covariance matrix. In the continuous data case, KL transform is a spectral decomposition of the covariance function. PCA is sometimes called the discrete KL transform.  

### Batch Norm

[Machine learning mastery blog](https://machinelearningmastery.com/batch-normalization-for-training-of-deep-neural-networks/)  

**Internal covariate shift** : Change in distribution of inputs to layers. Eg: different distributions coming up for each mini batch in mini batchGD.  

Batch Norm is a technique to reduce the internal covariance shift.  

- stabilizes the learning process
- drastically reduces the number of epochs

How to standardize?

- calc mean and std deviation of each inp variable in a batch
- it is normal to allow each layer to learn two more parameters, beta and gamma (new mean and std deviation)

- Further, it may not be a good idea to use batch normalization and dropout in the same network.

- The reason is that the statistics used to normalize the activations of the prior layer may become noisy given the random dropping out of nodes during the dropout procedure.

## Object Detection

- we are trying to find regional proposals

Images have two things

- aspect ratio
- scale

- doing some kind of optimization
 

## Questions

Whats wrong with having a 
DL course:
https://github.com/chizhang529/cs231n  
http://cs231n.github.io/  
