---
title: Statistics | Endogeneity
author: Jingwei Gao
pubDatetime: 2025-03-05
postSlug: endog
featured: false
draft: false
tags:
  - statistics
  - regression
description: >
  Cov(x,u)≠0
---

<img src="/assets/endog-1.png">

## Table of contents

## 1. Classical Linear Model Assumptions

Classical linear model assumptions contain six assumptions (Wooldridge, 2016, p. 92):

1. Linear in parameters: $y=\mathbf{x}^T\beta + \epsilon$
2. Random sample with n independent observations $(\mathbf{x}_i, y_i)$ from the population
3. No perfect collinearity
4. Zero conditional mean: $E(\epsilon | \mathbf{x}) = 0$ for any $\mathbf{x}$
5. Homoskedastic errors: $Var(\epsilon | \mathbf{x}) = \sigma^2$ for any $\mathbf{x}$ (Wooldridge, 2016, p. 106)
6. Noramlity: $\epsilon$ is independent to $\mathbf{x}$ and $\epsilon \sim N(0, \sigma^2)$

Remarks:

- **When assumptions 1 ~ 4 hold, the OLS estimators are unbiased.**
  - $\epsilon \perp \mathbf{x}$ {stronger than} $E(\epsilon | \mathbf{x}) = 0$ {stronger than} $Cov(\epsilon, \mathbf{x}) = \mathbf{0}$
  - If some of them are violated, the OLS estimators are often biased: A common case of violating Assumption 2 is time series data—observations of the same unit over time—where nearby observations are correlated. When Assumption 2 is violated, unbiasedness is not guaranteed.
- **When assumptions 1 ~ 5 (G-M) hold, the OLS estimators are BLUE.**
  - Best Linear Unbiased Estimators
  - Hence, assumption 5 serves two purposes: 1) to simplify the mathematical representation of the variance of estimators, and 2) to make estimators “efficient,” without affecting unbiasedness.
  - A linear estimator means that it can be represented as a linear combination of dependent variables.
  - Assumptions 1 ~ 5 together are referred to as the Gauss-Markov Assumptions.
- **When assumptions 1 ~ 6 (CLM)are satisfied, the OLS estimators are MVUE.**
  - Minimum Variance Unbiased Estimators
  - Assumption 6 removes the constraint of “linear estimators.”
  - Assumption 6 → Assumption 4 + Assumption 5
  - Assumptions 1 ~ 5 together are referred to as the Classical Linear Model Assumptions.

## 2. Endogeneity: Cov(x,u)≠0

Under the [Modern Assumptions](https://www.youtube.com/watch?v=BDll385V8dY), OLS estimators are guaranteed to be **consistent** even when a weaker version of assumption 4 is maintained, which refers to no correlation between regressors and the error term: $Cov(\epsilon, \mathbf{x}) = \mathbf{0}$. Remember, this condition is weaker than $E(\epsilon | \mathbf{x}) = 0$.

We call a regressor **endogenous** when it is correlated with the error term. **In this case, the OLS estimators are neither guaranteed to be consistent nor unbiased.**

Example: [Omitted Variable Bias](https://are.berkeley.edu/courses/EEP118/current/handouts/OVB%20versus%20Multicollinearity_eep118_sp15.pdf) - When we miss the variable correlated with both included independent variables and the dependent variable (aka. included independent variables correlate with the error term), the estimated coefficients of the included variables would be also distorted (i.e. inconsistent).

## 3. Dealing with Endogeneity

- [Fixed Effects Model](https://lobbyboy-dray.github.io/posts/fixed-effects-model/)
- [Instrument Variable](https://lobbyboy-dray.github.io/posts/instrument/)
- [Difference in Differences](https://lobbyboy-dray.github.io/posts/crash-course-causal-inf/)

## 4. References

- Wooldridge, J. M. (2013). _Introductory Econometrics : a Modern approach._ (5th ed.). Mason Cengage Learning.
- [楊睿中【統計學 2021】Asymptotic Theory for Linear Regression -- 04. Consistency of OLS](https://www.youtube.com/watch?v=BDll385V8dY)
- 【OVB】[Microsoft Word - OVB versus Multicollinearity_eep118_sp15.docx (berkeley.edu)](https://are.berkeley.edu/courses/EEP118/current/handouts/OVB%20versus%20Multicollinearity_eep118_sp15.pdf)
- 【OVB】[14310x*Lecture_Slides_20\_\_2*.pdf (mit.edu)](https://openlearninglibrary.mit.edu/assets/courseware/v1/dbcda49448cf5453bfe40b3181f77699/asset-v1:MITx+14.310x+2T2021+type@asset+block/14310x_Lecture_Slides_20__2_.pdf)
