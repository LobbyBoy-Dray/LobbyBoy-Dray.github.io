---
title: >
  Method | Fixed Effects Model
author: Jingwei Gao
pubDatetime: 2024-03-20
postSlug: fixed-effects-model
featured: false
draft: false
hidden: false
tags:
  - regression
  - panel_data
description: >
  The fixed effects model aims to address a class of endogeneity issues characterized by the presence of unobserved variables correlated with both independent and dependent variables but constant over time.
---

## Table of contents

## 1. Motivation

- The fixed effects model aims to address a class of endogeneity issues characterized by the presence of unobserved variables that are **correlated with both the independent and dependent variables**, and are assumed to **remain constant over time**.
- For example, **different states** in America may have **different age distributions**, but the age distribution within each state tends to be relatively stable and does not change over time.
- Failure to include these variables may lead to **omitted variable bias**.
- We need **panel data** in this case.

## 2. Model Specification

$$
\begin{align*}
y_{it} &= \mathbf{x}_{it}^T \beta + \epsilon_{it} \\
&= \mathbf{x}_{it}^T \beta + \alpha_i + u_{it}
\end{align*}
$$

- $Cov(\mathbf{x}_{it}, \epsilon_{it}) \ne \mathbf{0}$ because of $Cov(\mathbf{x}_{it}, \alpha_{i}) \ne \mathbf{0}$, but $Cov(\mathbf{x}_{it}, u_{it}) = \mathbf{0}$
- In this model, $\alpha_i$ represents an **unobserved effect**, which is also referred to as a **fixed effect**, which means that it is fixed over time. $u_{it}$ is called the **idiosyncratic error**.
- ​Next​: how to estimate $\beta$ in this case? what additional assumptions are needed?

## 3. Model Estimation

Let:

$$
\bar{y}_i = \frac{1}{T}\sum_{t=1}^T y_{it}，\bar{\mathbf{x}}_i = \frac{1}{T}\sum_{t=1}^T \mathbf{x}_{it}，\bar{u}_i = \frac{1}{T}\sum_{t=1}^T u_{it}
$$

Then:

$$
y_{it}-\bar{y}_i = (\mathbf{x}_{it}-\bar{\mathbf{x}}_i)^T \beta + (u_{it}-\bar{u}_i)
$$

Hence, we can regress $y_{it} - \bar{y}_i$ on $\mathbf{x}_{it}-\bar{\mathbf{x}}_i$. The fixed effects estimator is also called the **within estimator**. We can also formulate the model into following form:

$$
\begin{align*} y_{it}&=\mathbf{x}^T_{it}\beta + \alpha_i + u_{it}\\ &=\mathbf{x}^T_{it}\beta + \alpha_1 + (\alpha_2-\alpha_1)I(i=2)+ \cdots + (\alpha_n-\alpha_1)I(i=n) + u_{it} \end{align*}
$$

In this case, we can regress $y_{it}$ on $\mathbf{x}^T_{it}, I(i=2), I(i=3), ... , I(i=n)$. This is called the **least squares dummy variables regression (LSDV)** . It can be proved that within estimator is identical to LSDV estimator. However, using LSDV can provide us with the estimation of fixed effects for each entity.

## 4. Strong Exogeneity

In order to get consistent estimators in FEM case, $\mathbf{x}$ need to be **strictly exogenous**:

$$
Cov(\mathbf{x}_{it}, u_{it}) = \mathbf{0}\\
Cov(\mathbf{x}_{i\tau}, u_{it}) = \mathbf{0}\quad \text{for}\ \tau = 1,2,..., T
$$

This is a pretty strong assumption!

## 5. Time Fixed Effects & Entity Fixed Effects

What we have discussed pertains to what we call **entity fixed effects**, where the fixed effects remain constant over time but vary across entities. It can be expressed as $\alpha_i$ in the model. Another type of fixed effects is **time fixed effects**, where the fixed effects remain constant across entities but vary over time. The model just changes the entity fixed effects $\alpha_i$ into time fixed effects $\gamma_t$:

$$
y_{it} = \mathbf{x}_{it}^T \beta + \gamma_t+ u_{it}
$$

The estimation approach is identical to that of the model with entity fixed effects.

However, we should remember that although we employ an entity and time fixed effects model, we are still unable to completely eliminate all omitted variable bias, as there may still be some omitted variables that vary across entities **and** change over time.

## 6. R Example

- [10.1 Panel Data](https://www.econometrics-with-r.org/10.1-panel-data.html) ~ [10.6 Drunk Driving Laws and Traffic Deaths](https://www.econometrics-with-r.org/10.6-drunk-driving-laws-and-traffic-deaths.html)​
- [10.7 Exercises | Introduction to Econometrics with R](https://www.econometrics-with-r.org/10.7-exercises-10.html)

## 7. References

- [楊睿中【統計學】An Introduction to Panel Models -- 01. Fixed Effects Models](https://www.youtube.com/watch?v=y3P_NTrtsAw)
- [10 Regression with Panel Data | Introduction to Econometrics with R](https://www.econometrics-with-r.org/10-rwpd.html)

‍
