---
title: Statistics | Robust Standard Errors
author: Jingwei Gao
pubDatetime: 2024-04-07
postSlug: rse
featured: false
draft: false
tags:
  - regression
  - statistics
description: A brief recap about robust💪 standard errors.
---

## 1. Motivation

In Classical Linear Model Assumptions, we make a strong assumption about the covariance matrix of error terms:

$$
\Sigma = \sigma^2I_n
$$

- Homoskedasticity (同方差): equal variance = the same value on diagonal
- No-autocorrelation (无自相关): zero covariance between different error terms = zero for non-diagonal values

If this assumption is violated, **the OLS estimators remain unbiased**. However, the expression for the estimator variance changes, rendering the values calculated using the **original standard error formula incorrect**. Hence, hypothesis tests conducted using these "SE" values become invalid. In this case, we will use robust standard errors instead.

## 2. How to Calculate

```python
# Specify the model and Estimate
model <- ...

# t-test for coefficients using standard errors
summary(model)

# t-test for coefficients using robust standard errors "HC0" (i.e. White standard errors)
coeftest(model, vcov = vcovHC, type = "HC0")
```

## 3. References

- 整齐归类：[计量经济学精华笔记（二）：异方差和自相关 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/242140022)
- 一些警告：[13. Robust Standard Errors - YouTube](https://www.youtube.com/watch?v=j3Sxbkd9iIs)
- 简单例子：[Heteroskedasticity Robust Standard Errors in R · r-econometrics](https://www.r-econometrics.com/methods/hcrobusterrors/)
- 更加复杂：[Understanding Robust Standard Errors | UVA Library (virginia.edu)](https://library.virginia.edu/data/articles/understanding-robust-standard-errors)
