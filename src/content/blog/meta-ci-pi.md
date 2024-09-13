---
title: Meta-Analysis | CI and PI in Meta-Analysis
author: Jingwei Gao
pubDatetime: 2024-08-01
postSlug: meta-ci-pi
featured: false
draft: false
tags:
  - meta-analysis
description: >
  Confidence Interval & Prediction Interval
---

## Table of contents

## 1. Confidence Interval & Prediction Interval

<img src="/assets/me-ci-pi.png" width="550">

- A **confidence interval (CI)** is an interval which is expected to _typically_ (like 95%) contain the **parameter** being estimated. Its counterpart is point estimation.
- A **prediction interval (PI)** is an interval which is expected to _typically_ (like 95%) contain a **future observation**, given what has already been observed.
- A parameter is considered **unknown but fixed**. In contrast, a future observation is usually regarded as **random**.
- In most cases, a CI is employed to estimate **the mean of a random variable**, whereas a PI is used to forecast **the value of the next sample from that random variable**.
- For instance, in linear regression, a CI is constructed for $E(y_i|x_i)$, while a prediction interval is established for $y_i|x_i$. In this context, the PI is typically broader than the CI, assuming the same level of confidence.

## 2. CI & PI in Meta-Analysis

In the REM case,

$$
\hat{\theta} =(\sum_jw_j\hat{\theta_j}) / (\sum_j w_j)\\
se(\hat{\theta}) = \sqrt{1/\sum_j w_j}
$$

CI for $\theta$,

$$
\hat{\theta}\ \pm\ c\ \times\ se(\hat{\theta})
$$

We use $\theta_k - \hat{\theta}$ to construct PI for $\theta_k$,

$$
E(\theta_k - \hat{\theta}) = E(\theta+u_k) - E(\hat{\theta}) = E(u_k) = 0 \\
V(\theta_k - \hat{\theta}) = V(\theta_k) + V(\hat{\theta}) = \tau^2 + V(\hat{\theta})\\
\hat{V}(\theta_k - \hat{\theta}) = \hat{\tau}^2 + se(\hat{\theta})^2
$$

Hence, PI for $\theta_k$,

$$
\hat{\theta}\ \pm\ c\ \times\ \sqrt{\hat{\tau}^2 + se(\hat{\theta})^2}
$$

We can see that PI for $\theta_k$ is broader than CI for $\theta$.

## 3. References

- [c17.pdf (introduction-to-meta-analysis.com)](https://introduction-to-meta-analysis.com/download/c17.pdf)
- [Prediction interval in random-effects meta-analysis](<https://www.ajodo.org/article/S0889-5406(20)30001-9/fulltext>)
- [Frontiers | Message Design Choices Don't Make Much Difference to Persuasiveness and Can't Be Counted On—Not Even When Moderating Conditions Are Specified (frontiersin.org)](https://www.frontiersin.org/journals/psychology/articles/10.3389/fpsyg.2021.664160/full)
