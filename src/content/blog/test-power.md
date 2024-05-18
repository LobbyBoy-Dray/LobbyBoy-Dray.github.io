---
title: Statistics | Power of Hypothesis Test
author: Jingwei Gao
pubDatetime: 2024-05-10
postSlug: test-power
featured: false
draft: false
tags:
  - statistics
description: The power of a test is the probability that the test correctly rejects the H0 when alternative hypothesis H1 is true.
---

## Table of contents

## Type I Error & Type II Error

Type I error occurs when the null hypothesis H0 is rejected even though it is actually true:

$$
P(\text{Type I error}) \triangleq P(\text{reject}\ H_0|H_0\ \text{true}) \triangleq \alpha
$$

Type II error occurs when the null hypothesis H0 is not rejected even though it is actually false:

$$
P(\text{Type II error}) \triangleq P(\text{accept}\ H_0|H_0\ \text{false}) \triangleq \beta
$$

We face a trade-off between Type I error and Type II error when constructing a test:

<img src="/assets/power-1.jpeg" width="600">

Therefore, when constructing a hypothesis test, it is typical to first set the probability of Type I error occurring, i.e. the significance level of the test, such as 0.05. Subsequently, based on this value, we calculate the threshold for a particular statistic, resulting in a specific hypothesis test.

Conversely, when provided with a specific test, we can calculate its significance level based on its threshold, representing the probability of committing a Type I error.

## Power

The power of a test is the probability that the test correctly rejects the H0 when alternative hypothesis H1 is true. It is the reverse of the probability of Type II error:

$$
\text{Power} = 1-\beta
$$

<img src="/assets/power-2.jpeg" width="500">

However, you may wonder how we can determine the power of our test, given that we lack knowledge of the true parameter under H1. In most cases, H1 contains an infinite number of possible values of the parameter (for example, μ>172 implies μ could be 173, 174, and so forth).

Nonetheless, we can construct the hypothesis test so that it has “enough power” to reject the H0 in favor of **values of the parameter under the H1 that are scientifically meaningful**. In other words, we can ensure that when the true parameter falls within, for example, the range of 171 to 180, the power of our hypothesis test will always exceed a certain threshold.

## Power Function

The power of a hypothesis test depends on the value of the parameter being examined:

<img src="/assets/power-3.jpeg" width="500">

Hence, we can draw a plot like that - it is the **power function**:

<img src="/assets/power-4.jpeg" width="300">

Altering the significance level α will cause the curve to move either upwards or downwards:

<img src="/assets/power-5.jpeg" width="500">

Increasing the sample size N can decrease the α and β simultaneously by providing a more narrow sampling distribution:

<img src="/assets/power-6.jpeg" width="500">

In summary, the power of a test is mainly associated with three factors: 1) the true parameter (power function); 2) the significance level α (trade-off); 3) the sample size N (larger is always better).

## References

- [Lesson 25: Power of a Statistical Test | STAT 415](https://online.stat.psu.edu/stat415/lesson/25). Penn State University.
- 陈希孺. (2009). 《概率论与数理统计》, 第5.1节.
- [显著性测试的功效介绍 | 可汗学院](https://www.youtube.com/watch?v=6_Cuz0QqRWc).
- Imai, K. (2017). Quantitative Social Science: An Introduction, Chapter 7.2.6, Power Analysis.
