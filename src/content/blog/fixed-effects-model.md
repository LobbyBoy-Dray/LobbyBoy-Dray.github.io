---
title: >
  Method | Fixed Effects Model & Random Effects Model
author: Jingwei Gao
pubDatetime: 2024-03-20
postSlug: fixed-effects-model
featured: false
draft: false
hidden: false
tags:
  - regression
  - panel_data
  - causality
description: >
  Fixed effects model aims to address a class of endogeneity issues, while random effects model aims to address the issue of autocorrelated errors.
---

## Table of contents

## 1. Fixed Effects Model

### 1.1 Motivation

- The fixed effects model aims to address a class of endogeneity issues characterized by the presence of unobserved variables that are **correlated with both the independent and dependent variables**, and are assumed to **remain constant over time**.
- For example, **different states** in America may have **different age distributions**, but the age distribution within each state tends to be relatively stable and does not change over time.
- Failure to include these variables may lead to **omitted variable bias**.
- We need **panel data** in this case.

### 1.2 Model Specification

$$
\begin{align*}
y_{it} &= \mathbf{x}_{it}^T \beta + \epsilon_{it} \\
&= \mathbf{x}_{it}^T \beta + \alpha_i + u_{it}
\end{align*}
$$

- In this model, $\alpha_i$ represents **unobserved time-invariant variables**, which are also referred to as the **fixed effect** in the context of fixed effects models. $u_{it}$ is called the **idiosyncratic error**, which represents **unobserved time-variant variables.**
- Model settings: $Cov(\mathbf{x}_{it}, \alpha_{i}) \ne \mathbf{0}$, leading to $Cov(\mathbf{x}_{it}, \epsilon_{it}) \ne \mathbf{0}$.
- Next: how to estimate $\beta$ in this case? what additional assumptions are needed? We first illustrate the estimation process, which is application-oriented, and then briefly discuss the assumptions required, focusing on the theoretical aspects.

### 1.3 Model Estimation

Two different estimation processes lead to two different but equivalent estimators.

**1) Within Estimator**

Let:

$$
\bar{y}_i = \frac{1}{T}\sum_{t=1}^T y_{it}，\bar{\mathbf{x}}_i = \frac{1}{T}\sum_{t=1}^T \mathbf{x}_{it}，\bar{u}_i = \frac{1}{T}\sum_{t=1}^T u_{it}
$$

Then:

$$
y_{it}-\bar{y}_i = (\mathbf{x}_{it}-\bar{\mathbf{x}}_i)^T \beta + (u_{it}-\bar{u}_i)
$$

Hence, we can regress $y_{it} - \bar{y}_i$ on $\mathbf{x}_{it}-\bar{\mathbf{x}}_i$. The fixed effects estimator is also called the within estimator.

**2) LSDV Estimator**

We can also formulate the model into following form:

$$
\begin{align*} y_{it}&=\mathbf{x}^T_{it}\beta + \alpha_i + u_{it}\\ &=\mathbf{x}^T_{it}\beta + \alpha_1 + (\alpha_2-\alpha_1)I(i=2)+ \cdots + (\alpha_n-\alpha_1)I(i=n) + u_{it} \end{align*}
$$

In this case, we can regress $y_{it}$ on $\mathbf{x}^T_{it}, I(i=2), I(i=3), ... , I(i=n)$. This is called the **least squares dummy variables regression (LSDV)** . It can be proved that within estimator is identical to LSDV estimator.

### 1.4 Strong Exogeneity

In order to get consistent estimators in FEM case, $\mathbf{x}$ need to be **strictly exogenous**:

$$
Cov(\mathbf{x}_{it}, u_{it}) = \mathbf{0}\\Cov(\mathbf{x}_{i\tau}, u_{it}) = \mathbf{0}\quad \text{for}\ \tau = 1,2,..., T
$$

This is a pretty strong assumption!

### 1.5 Time Fixed Effects & Entity Fixed Effects

What we have discussed pertains to what we call **entity fixed effects**, where the fixed effects remain constant over time but vary across entities. It can be expressed as $\alpha_i$ in the model. Another type of fixed effects is **time fixed effects**, where the fixed effects remain constant across entities but vary over time. The model just changes the entity fixed effects $\alpha_i$ into time fixed effects $\gamma_t$:

$$
y_{it} = \mathbf{x}_{it}^T \beta + \gamma_t+ u_{it}
$$

The estimation approach is identical to that of the model with entity fixed effects.

However, we should remember that although we employ an entity and time fixed effects model, we are still unable to completely eliminate all omitted variable bias, as there may still be some omitted variables that vary across entities **and** change over time.

### 1.6 R Example

- [10.1 Panel Data](https://www.econometrics-with-r.org/10.1-panel-data.html) ~ [10.6 Drunk Driving Laws and Traffic Deaths](https://www.econometrics-with-r.org/10.6-drunk-driving-laws-and-traffic-deaths.html)
- [10.7 Exercises | Introduction to Econometrics with R](https://www.econometrics-with-r.org/10.7-exercises-10.html)

## 2. Random Effects Model

### 2.1 Motivation

- The random effects model aims to address the issue of **autocorrelated error terms**, which arises because the error term includes one component that does not vary over time, leading to correlation of the error terms within the same entity.
- Autocorrelated error terms **do not affect unbiasedness or consistency**, but it does **make the standard errors of OLS estimates and other test statistics invalid**, thereby rendering various statistical tests unreliable.

### 2.2 Model Specification

The model form is the same as that of the fixed effects model:

$$
\begin{align*}
y_{it} &= \mathbf{x}_{it}^T \beta + \epsilon_{it} \\
&= \mathbf{x}_{it}^T \beta + \alpha_i + u_{it}
\end{align*}
$$

However, the settings are quite different:

- In this model, $\alpha_i$ also means unobserved time-invariant variables but it represents **one part of the error term** $\epsilon_{it}$——the time-invariant part. $u_{it}$ represents the time-variant part of the error term.
- In this context, some people say that one main difference between the Fixed Effects Model and the Random Effects Model is that the former treats $\alpha_i$ as **fixed**, while the latter treats $\alpha_i$ as **random**.
- Besides: $Cov(\mathbf{x}_{it}, \alpha_{i}) = \mathbf{0}$ and this is another main difference with FEM. It means that REM is not designed for dealing with endogeneity issues caused by unobserved variables. Instead, it is for addressing the autocorrelated errors problem.

### 2.3 Model Estimation

Why are the error terms correlated here? let $Var(\alpha_i) = \sigma_{\alpha}^2,\ Var(u_{it}) = \sigma_{u}^2$

$$
\begin{align*}
Cov(\epsilon_{it}, \epsilon_{is}) &= Cov(\alpha_i + u_{it},\ \alpha_i + u_{is}) \\
&= Var(\alpha_i) + Cov(u_{it}, u_{is}) \\
&= \sigma_{\alpha}^2 + 0\\
&= \sigma_{\alpha}^2
\end{align*}
$$

We can see that the error terms from different times within the same individual are correlated. Hence, we have to resort to GLS to get a good estimate for $\beta$. The transformation multiplier for GLS is:

$$
\lambda = 1 - \frac{\sigma_{\alpha}}{\sqrt{\sigma^2_{\alpha} + T\sigma^2_u}}
$$

Then do the data transformation:

$$
y_{it}-\lambda \bar{y}_i = (\mathbf{x}_{it}-\lambda  \bar{\mathbf{x}}_i)^T \beta + (\epsilon_{it}-\lambda  \bar{\epsilon}_i)
$$

We can use multiple ways to estimate λ and then transform the data. Finally, use OLS to do estimation on the transformed data, getting $\hat{\beta}^{RE}$.

## 3. References

- [楊睿中【統計學】An Introduction to Panel Models -- 01. Fixed Effects Models](https://www.youtube.com/watch?v=y3P_NTrtsAw)
- [10 Regression with Panel Data | Introduction to Econometrics with R](https://www.econometrics-with-r.org/10-rwpd.html)
- [《因果推断实用计量方法》，邱嘉平](https://book.douban.com/subject/35177003/)：第8章
- [《计量经济学导论》，伍德里奇](https://book.douban.com/subject/5068097/)：第10~12章
- [《量化数据分析：通过社会研究检验想法》，Donald J. Treiman](https://book.douban.com/subject/11578220/)：第15章
