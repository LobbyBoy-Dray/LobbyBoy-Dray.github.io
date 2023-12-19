---
title: Method | Analysis of Variance
author: Jingwei Gao
pubDatetime: 2023-10-21T13:43:52+08
postSlug: anova-family
featured: true
draft: false
tags:
  - regression
description: >
  One-way ANOVA; two-way ANOVA without replication; two-way ANOVA with replication; ANCOVA; MANOVA → All about ANOVA.
---

|             Type              | Regression Formula |   Y    |    X     |     M      |
| :---------------------------: | :----------------: | :----: | :------: | :--------: |
|         One-way ANOVA         |       Y ~ X        | scalar | discrete |     /      |
| Two-way ANOVA (× replication) |     Y ~ X + M      | scalar | discrete |  discrete  |
| Two-way ANOVA (√ replication) | Y ~ X + M + X \* M | scalar | discrete |  discrete  |
|            ANCOVA             | Y ~ X + M + X \* M | scalar | discrete | continuous |
|            MANOVA             |       Y ~ X        | vector | discrete |     /      |

## Table of contents

## 1. One-way ANOVA

> Y ~ X

### 1.1 Experiment design

In a location that has never grown wheat before, wheat cultivation begins with **selecting varieties (品种) through field experiments**. There are $k$ varieties and $n$ experimental fields - each variety occupies $n_i$ fields, which allows for the estimation of random errors.

Once the number of fields for each variety is determined, the fields are allocated using a **randomization (随机化)** method - This prevents the case that a variety is assigned to a particularly fertile field, leading to higher yields unrelated to the variety’s qualities.

The objective is to compare the yield differences among the various wheat varieties and **identify the most suitable variety** for cultivation in the region. Here,

- “variety (品种)” is referred to as a “**factor (因素)**”
- “each specific variety (具体的某个品种)” = “a specific **level (水平)** of that factor”
- Thus, this experiment is called a **single-factor experiment with k levels (单因素k水平)**.

### 1.2 Model specification

Let $Y_{ij}$ denote the j-th observation of the i-th level:

- variety 1: $Y_{11}$, $Y_{12}$, ..., $Y_{1n_1}$
- variety 2: $Y_{21}$, $Y_{22}$, $Y_{23}$, ..., $Y_{2n_2}$
- ...

Our model:

$$
Y_{ij} = a_i + e_{ij} \quad (i=1 \sim k,\ j=1 \sim n_i)
$$

- $a_i$: mean of the level i
- $e_{ij}$: random error with mean 0 and fixed variance

Our question / null hypothesis:

$$
a_1=a_2=\cdots=a_k
$$

Assumptions:

- Observations within each level/group must be independent and identically distributed.
- The response variable should be normally distributed for each group.
- All groups should have the same variance.

### 1.3 Theoretical analysis

Why $Y_{ij}$ vary?

- Variation source 1: $a_i$，即不同组之间本身水平就有差异。
- Variation source 2: $e_{ij}$，即随机误差一直存在。
- An intuitive idea is to **decompose the total variation** into these two parts, to see if the source of variation 1 is sufficiently large.

Total variation (Y_bar = average across all individuals):

$$
SS = \sum_{i=1}^k \sum_{j=1}^{n_i}(Y_{ij}-\bar{Y})^2
$$

Variation due to random error (i.e. 组内变异):

$$
SSE = \sum_{i=1}^k \sum_{j=1}^{n_i}(Y_{ij}-\bar{Y}_i)^2
$$

Variation due to different level (i.e. 组间变异):

$$
SSA = SS - SSE = \sum_{i=1}^k n_i(\bar{Y}_i-\bar{Y})^2
$$

### 1.4 Full test: a1=a2=...=ak?

When the proportion of SSA in the SS is higher, it is easier to reject our previously stated H0. Therefore, the test statistic we choose is:

$$
\frac{SSA}{SSE}
$$

When the random error term follow a normal distribution and H0 is true, it follows that:

$$
\frac{SSA/(k-1)}{SSE/(n-k)} \sim F_{k-1,n-k}
$$

Set the test level at $1-\alpha$. Accept H0 when:

$$
\frac{MSA}{MSE} \le F_{k-1,n-k}(\alpha)
$$

, otherwise reject H0. Note, if the test result is significant (reject H0), then there is reason to believe that a1~ak are **not all the same** — but this does not necessarily mean that none of them are the same.

### 1.5 Partial test: a_u=a_v?

We can test whether $a_u=a_v$ by calculating the confidence interval (CI) for $a_u - a_v$.

$$
\bar{Y}_u = a_u + \bar{e}_u \sim N(a_u,\frac{\sigma^2}{n_u}) \\
\bar{Y}_v = a_v + \bar{e}_v \sim N(a_v,\frac{\sigma^2}{n_v})
$$

Hence, the point estimate of $a_u-a_v$, $\bar{Y}_u - \bar{Y}_v$, follows:

$$
N(a_u-a_v, (\frac{1}{n_u}+\frac{1}{n_v})\sigma^2)
$$

Then we have:

$$
\frac{(\bar{Y}_u-\bar{Y}_v)-(a_u-a_v)}{\sqrt{\frac{1}{n_u}+\frac{1}{n_v}}×\sigma} \sim N(0,1)
$$

MSE is an unbiased estimate of $\sigma^2$. Substitute $\hat{\sigma}$ for $\sigma$:

$$
\frac{(\bar{Y}_u-\bar{Y}_v)-(a_u-a_v)}{\sqrt{\frac{1}{n_u}+\frac{1}{n_v}}×\hat{\sigma}} \sim t(n-k)
$$

We can construct a CI for $a_u-a_v$ with a confidence coefficient of $1-\alpha$. If 0 is not within this CI, then it indicates that there is indeed a significant difference between $a_u$ and $a_v$.

### 1.6 Connection with regression

Single-factor (k levels) ANOVA is equivalent to a linear regression with one categorical variable that can take k values. Thus, it can be transformed into k-1 dummy variables included in the linear regression model.

## 2. Two-way ANOVA

### 2.1 Without replication

> Y ~ X + M

<img src="/assets/two-way-anova-without.png" alt="two-way-anova-without" width="600"/>

In this case, we have two factors: factor A has $k_1$ levels and factor B has $k_2$ levels. The total number of unique combinations of these factors is $k_1 × k_2$. Unlike the previous setting where multiple experiments ($n_i$) are conducted for each level of a single factor, here we conduct only one experiment for each combination of Factor A and Factor B levels.

Our model:

$$
Y_{ij} = \mu + a_i + b_j + e_{ij} \quad (i=1 \sim k_1,\ j=1 \sim k_2)
$$

- $\mu$: total average
- $a_i$: increment due to level i of factor A
- $b_j$: increment due to level j of factor B

Our null hypotheses:

$$
H_{0A}: a_1 = a_2 = \cdots = a_{k_1} = 0\\
H_{0B}: b_1 = b_2 = \cdots = b_{k_2} = 0
$$

Variance decomposition:

$$
SS = SSA + SSB + SSE \\
SS = \sum_{i=1}^{k_1} \sum_{j=1}^{k_2}(Y_{ij}-\bar{Y})^2\\
SSA = \sum_{i=1}^{k_1} k_2 (\bar{Y}_{i*}-\bar{Y})^2\\
SSB = \sum_{j=1}^{k_2} k_1 (\bar{Y}_{*j}-\bar{Y})^2\\
$$

Total variation is caused by variation among levels in factor A, variation among levels in factor B, and random error. The test statistics are similar to those in the single-factor setting: the larger the $MSA/MSE$, the more probable it is to reject the null hypothesis $H_{0A}$ that factor A does not have a significant impact on DV.

**Why we do two-way ANOVA without replication?** Introducing a new factor (i.e. independent variable) can further decompose the SS, resulting in a smaller SSE; thus, group differences that were not detectable in one-way ANOVA might be detected here.

### 2.2 With replication

> Y ~ X + M + X\*M

<img src="/assets/two-way-anova-with.png" alt="two-way-anova-with" width="600"/>

Besides reducing SSE, a two-way ANOVA with replication is mainly used to detect **interaction effects** between two factors:

![two-way-anova-with2](/assets/two-way-anova-with2.png)

- x-axis = Factor A (main IV); y-axis = DV; line-color = Factor B (group IV)
- We would like to understand whether the effect of one factor (Factor A) on the DV varies across different levels of another factor (Factor B).
- We only focus on the significance of interaction term.

## 3. ANCOVA

> Y ~ X + M + X\*M (M is a continuous variable)

One-way ANOVA is used to evaluate whether significant differences exist among the outcomes of various treatments (i.e different levels within a factor). Two-way ANOVA combines two factors within a single study to examine not only the main effects of each factor but also how they interact with each other. To sum up, ANOVA serves as a statistical approach where the **predictors are discrete variables** (categorical or ordinal). What if we have both discrete and continuous predictors? In such cases, we use ANCOVA.

We can understand the motivation for ANCOVA from two perspectives. First, it can be seen as an improved version of the two-way ANOVA with replication, suitable for a continuous predictor. Thus, its motivation is the same as that of the two-way ANOVA with replication. Second, as indicated by its name, incorporating covariates, or control variables, allows for the exclusion of certain confounding factors.

For more details, please refer to [ANCOVA (Analysis of Covariance) in SPSS](/ANCOVA-SPSS.pdf).

## 4. MANOVA

ANOVA only has one DV, whereas in MANOVA, there are multiple dependent variables. One possible scenario is that performing MANOVA reveals significant differences between groups, but when conducting separate ANOVAs for each dependent variable, no significant differences are found among the groups:

![manova](/assets/manova.png)

ANOVA:

$$
Y_{ij} \sim N(a_i, \sigma^2)
$$

MANOVA:

$$
\vec{Y}_{ij} \sim N(\vec{a}_i, \Sigma)
$$

MANOVA requires checking the homogeneity of the covariance matrices: While ANOVA assumes that the variances of the error terms are the same across all groups, in MANOVA this assumption extends to the covariance matrices. Box's M test can be used to verify this.

## Reference

- [陈希孺&倪国熙: 数理统计学教程](https://book.douban.com/subject/3996242/)
- [Statistics 101: ANOVA](https://www.youtube.com/watch?v=0Vj2V2qRU10&list=PLIeGtxpvyG-KA-BLkL391X__r0kU4_hm5)
- [MANOVA - explained with a simple example](https://www.youtube.com/watch?v=CBXYxs9pLW8)
- [APS 240: Data Analysis and Statistics with R](https://dzchilds.github.io/stats-for-bio/)
- [ANCOVA (Analysis of Covariance) in SPSS](/ANCOVA-SPSS.pdf)
