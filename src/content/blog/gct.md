---
title: >
  Method | Granger Causality Test
author: Jingwei Gao
pubDatetime: 2024-05-01
postSlug: gct
featured: false
draft: false
hidden: false
tags:
  - regression
  - causality
  - time_series
description: >
  Granger causality testing is a statistical method used to assess whether one time series has forcasting or predictive power over another.
---

## Table of contents

## 1. Terminology

Granger causality testing is a statistical method used to assess whether one time series has **forcasting or predictive power** over another.

The AR model uses past values of Y to forecast its current value. If the predictive power significantly improves after adding past values of X, we say that **X Granger-causes Y**, which means the following holds in general:

$$
E(y_t | I_{t-1}) \ne E(y_t | J_{t-1})
$$

where $I_{t-1}$ represents all past information of both Y and X, and $J_{t-1}$ represents only the past information of Y.

However, it is important to note that Granger causality only indicates a **temporal relationship** between the variables, not a causal one.

## 2. Steps

Assume $\{x_t\}$ and $\{y_t\}$ are two **stationary** time series. Three steps:

**1) Determine the order p** for the AR model of $\{y_t\}$:

$$
y_t = \beta_0 + \sum_{i=1}^p \beta_iy_{t-i} + u_t
$$

**2) Test the joint significance** of $x_{t-j}$:

$$
y_t = \beta_0+ \sum_{i=1}^p \beta_i y_{t-i} + \sum_{j=1}^q \delta_j x_{t-j} + u_t
$$

**3) Make the conclusion:** If we reject H0, it indicates that X is a Granger cause of Y; otherwise, it is not.

Some remarks:

- ⚠️⚠️⚠️The prerequisite of GCT is that all time series are **stationary**.
- As long as any lagged term of X is significant, X is a Granger cause of Y.
- The choice of lag _q_ for X is not important, but there are some empirical guidelines. For instance, for annual data, 1 to 2 lagged terms may suffice; for quarterly data, 4 or 8; for monthly data, 6 or 12.
- In practice, when testing for Granger causality between X and Y, four possible outcomes may appear: ①only X is a Granger cause of Y, ②only Y is a Granger cause of X, ③both X and Y are Granger causes of each other, or ④there is no Granger causality between X and Y.
- Multivariate GCT = VAR

## 3. References

- Wooldridge, J. M. (2013). _Introductory Econometrics: A Modern Approach_ (5th ed.). Mason, OH: Cengage Learning. Chapter 18.
- [Granger Causality : Time Series Talk - YouTube](https://www.youtube.com/watch?v=b8hzDzGWyGM)
- [Granger causality - Wikipedia](https://en.wikipedia.org/wiki/Granger_causality)
- [https://mp.weixin.qq.com/s/AvJpXnzP4FYMv5vTohC74w](https://mp.weixin.qq.com/s/AvJpXnzP4FYMv5vTohC74w)
