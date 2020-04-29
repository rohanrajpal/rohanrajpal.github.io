---
layout: post
title:  "[Draft] 30 days of leetcode"
date:   2019-11-15 16:00:00 +0530
categories: [Android, Design]
comments: true
---

https://www.johndcook.com/blog/2018/03/24/squared-digit-sum/

- write different approaches

## 3 : Maximum Subarray

- [Problem link](https://leetcode.com/explore/challenge/card/30-day-leetcoding-challenge/528/week-1/3285/)

initial ideas

let's calculate the prefix sum
[-2,1,-3,4,-1,2,1,-5,4]
[-2,-1,-4,0,-1,1,2,-3,1]

CTCI

- bottlenecks
- unnecessary work
- duplicate work


This problem
- kadane
- d and c