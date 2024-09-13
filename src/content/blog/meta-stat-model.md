---
title: Meta-Analysis | Three Models for Meta-Analysis
author: Jingwei Gao
pubDatetime: 2024-07-01
postSlug: meta-stat-model
featured: false
draft: false
tags:
  - meta-analysis
description: >
  Common-Effect Model; Fixed-Effects Model; Random-Effects Model
---

## Table of contents

## 1. Notations & The General Model

_K_ independent studies whose indexes are _j = 1, 2, …, K_:

- $\theta_j$ = the **true effect size** in study _j_ (unknown)
  - It is the effect size obtained when the sample size of the study _j_ is infinitely large.
- $\hat{\theta}_j$ = the **estimate** of $\theta_j$ in study _j_ (reported by authors)
  - So it contains the sampling error.
  - $\hat{\sigma}_j$ = the standard error of $\hat{\theta}_j$ in study _j_ (reported by authors)
- $\theta$ = the **mean effect size** of the population (unknown)
  - It represents the average effect size across an infinite number of studies.

The goal of a meta-analysis is to combine $\hat{\theta}_j$ in a single result to get good inference about $\theta$. Basically, there are three approaches to model the effect sizes. All of them can be considered as specified by the following model (each with its own assumptions):

$$
\hat{\theta}_j = \theta_j + \epsilon_j \ \quad j=1,2,\cdots,K
$$

where:

$$
\epsilon_j \sim N(0, \sigma_j^2)
$$

In this distribution, $\sigma_j^2$ is an unknown parameter. However, meta-analysis does not estimate them - it uses the estimated values $\hat{\sigma}_j^2$ provided by authors directly. In this case, we write:

$$
\epsilon_j \sim N(0, \hat{\sigma}_j^2)
$$

What is the connection between $\theta_j$ and $\theta$? After all, our ultimate goal is to estimate $\theta$. While each of these three models has its own interpretation of $\theta$ (due to different assumptions), they all essentially involve some form of $\theta_j$ aggregation:

$$
\theta = \text{weighted\_sum}(\theta_j)
$$

Thus, the estimate of $\theta$ also shares a similar form among three approaches, which is the weighted sum of $\hat{\theta}_j$:

$$
\hat{\theta} = \frac{\sum_{j=1}^K w_j \hat{\theta}_j}{\sum_{j=1}^K w_j}
$$

They only differ in how to define the weights $w_j$.

Be careful! The meanings of “fixed effects” and “random effects” here differ from those in the “fixed effects model” and “random effects model” used in panel data analysis (refer to [this post](https://zhuanlan.zhihu.com/p/60528092)).

## 2. Common-Effect Model

$$
\hat{\theta}_j = \theta + \epsilon_j
$$

- **CEM assumes** the true effect size is constant across all studies - hence, it is also equal to the mean effect size of the population: $\theta = \theta_1 = \theta_2 = \cdots = Ave(\theta_j)$
- CEM is best suited for use with pure replicate studies, as only in this case can we claim that the true effect size is the same in all studies.

## 3. Fixed-Effects Model

$$
\hat{\theta}_j = \theta_j + \epsilon_j
$$

- **FEM assumes** the collected studies in the meta-analysis define the entire population of interest. Hence, the true effect sizes vary between studies but are fixed: $\theta = Ave(\theta_j)$
- Some sources distinguish between CEM and FEM (e.g., [stata.com](https://www.stata.com/manuals/metaintro.pdf)), while others do not (e.g., [Introduction-to-Meta-Analysis.pdf](https://www.agropustaka.id/wp-content/uploads/2020/04/agropustaka.id_buku_Introduction-to-Meta-Analysis.pdf)). This is mainly because the two models produce the same estimate for the $\theta$.

## 4. Random-Effects Model

$$
\hat{\theta}_j = \theta_j + \epsilon_j = \theta + u_j + \epsilon_j \\

u_j \sim N(0, \tau^2)
$$

- **FEM assumes** the collected studies only consist of a random sample from a larger population of studies. Hence, $\theta_j$ varies across different studies and is also a random variable that can be separated into the mean effect size of the population $\theta$ and the deviation from it $u_i$.
- In this case, $\theta = E(\theta_j)$ - because $\theta_j$ is a random variable now.
- This is the most common scenario.

About $\tau^2$:

- $\tau^2$ characterizes the variation among the true effect sizes of all studies, or in other words, the **between-study variability**. Hence, it is called the **heterogeneity parameter**.
- $\tau^2$ can be estimated by many methods: **restricted maximum likelihood (REML)**, maximum likelihood (ML), **empirical Bayes (EB)**, DerSimonian–Laird (DL), Hedges (HE), Sidik–Jonkman (SJ), Hunter–Schmidt (HS).

## 5. Model Estimation

The universal method - **Inverse-variance estimation method**

- Common-effect model: $w_j = 1/\hat{\sigma}_j^2$
- Fixed-effects model: $w_j = 1/\hat{\sigma}_j^2$ (identical to the CEM)
- Random-effects model: $w_j = 1/(\hat{\sigma}_j^2+\hat{\tau}^2)$

In the REM case,

$$
\hat{\theta} =(\sum_jw_j\hat{\theta_j}) / (\sum_j w_j)\\
se(\hat{\theta}) = \sqrt{1/\sum_j w_j}
$$

Hence, we can construct the **confidence interval** or do **hypothesis testing** for $\theta$. That is what is called “the CI of the summary effect” in most meta-analyses.
