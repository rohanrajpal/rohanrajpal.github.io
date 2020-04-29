---
layout: post
title:  "RAdam"
date:   2020-02-14 16:00:00 +0530
categories: Deep Learning, Adaptive Learning rate
comments: true
---

# Links

https://www.reddit.com/r/MachineLearning/comments/csgbxl/d_rectified_adam_radam_a_new_state_of_the_art/

[reviews](https://openreview.net/forum?id=rkgz2aEKDr)


# What are adaptive learning rates

RMSProp, Adam

# RAdam

Problem with adaptive learning rates:  
- problematically large variance in the early stage
- 

# Doubts

- what is adaptive ratio?
- what is exponential moving average?
- what is chi-square distribution?
- 

# Medium blog

The summary though is the RAdam dynamically turns on or off the adaptive learning rate depending on the underlying divergence of the variance. In effect, it provides a dynamic warmup with no tunable parameters needed.  


# Insights from Reddit

In the original paper they apply what they call a "correction" to the procedure which calculates a smooth gradient and square gradient by accumulating them over multiple iterations. This correction compensates for the fact that the smoothing starts at 0, given that there isn't initially a history of gradients to draw from.

Perhaps you'd achieve a similar effect by only applying the correction to the accumulated square gradients. (Remember that the smooth gradient is divided by the sqrt of the smooth square gradient, acting like a frame of reference for what step sizes are appropriate, so you'd still want to use the correction for the smooth square gradients.) This would make it start at a standstill and gradually pick up speed, rather than giving huge initial weight to that very first iteration.

---------------------------

Q:why not? there are other adam-class algorithms like the one i have mentioned that produce "better" minima in terms of generalization. it should be obvious to compare them.  

From the authors own words: "The main benefit of RAdam is the robustness". An optimizer that is super robust to learning rate is pretty valuable, even if it doesn't exactly find the best minima. If you are trying to figure out if some training change or regularization helps your model, not having to worry about learning rate/momentum/ etc. for the optimizer is really useful, it allows you to orthogonalize your concerns.

If you already have your model finalized and you want that last .1% of test accuracy, then you can swap in SGD and hand tune the final few training parameters.  

-----------------------------

They didn't claim its SOTA, but a good read anyway

They make the same basic mistakes as every other ML paper claiming SOTA performance nowadays. The most glaring issue is, as always, in the experimental results, specifically tables 1 and 2. In these tables, the authors report results for three benchmarks, yet from the description of their setup (which includes section 5 and appendix B) it is not clear how exactly these numbers were obtained. Are they averages of multiple runs? If so, how many runs were done? If they're not averages but results from a "best-of-N" experiment (as I strongly suspect since this is extremely common practice), then in order for these results to make any statistical sense at all I would still need to know N. This isn't reported anywhere and so accurately gauging the statistical significance becomes impossible. Curiously, when they get to table 3 the authors apparently suddenly remembered what a standard error is.

Fortunately, the authors provide code to reproduce the experiments, so I could rerun their experiments again and see for myself how significant the differences are. However, I shouldn't have to be doing this; the authors should report standard deviations in their results or at least mention that it wasn't significant if they're going to leave it out altogether. Moreover, the code on their GitHub repository strongly suggests these results were obtained from just a single run. That is simply not sufficient to claim any sort of superior performance, especially when the improvements are sometimes less than 1%.

I'm also not convinced that benchmarking four data sets, two of which belong to the same domain, is sufficient to claim SOTA performance for a general-purpose optimizer. Although their theory about warm-up strategies sounds plausible and they have a theorem supporting it, it would be nice if the authors explored toy problems where RAdam yields provably better (or worse) solutions than Adam. Instead they go the usual ML route: explain the intuition, present the algorithm, test on a handful of academic benchmarks (preferably without reporting errors) and claim SOTA. Frankly, it's getting old.

--------------------------------

