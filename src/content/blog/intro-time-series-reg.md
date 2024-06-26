---
title: >
  Method | Introduction About Time Series Regression
author: Jingwei Gao
pubDatetime: 2024-03-18
postSlug: intro-time-series-reg
featured: false
draft: false
hidden: false
tags:
  - regression
  - time_series
description: >
  Note on ch. 10 to 12 of Wooldridge's Econometrics covering topics related to the classical and modern assumptions of time series regression, as well as the consequences, tests, and corrections of serial correlation problem.
---

## Table of contents

## 1. Introduction

> This section briefly discusses what time series data is, using the static model and the finite distributed lag model as two simple examples.

### 1.1. What is a Time Series?

- A **time series data set** consists of observations on a variable $\{x_t\}$ or several variables $\{(x_t,y_t)\}$ over time.
- A sequence of random variables whose index is time is called a **stochastic process** or **time series process**.
- Time series data contain information about **temporal ordering** - we cannot change the index among observations.
- The **sample size** of time series data is defined as the number of time points for observations.

### 1.2 Static Model

Static model describes a **synchronous** relationship:

$$
y_t = \beta_0 + \beta_1z_t + u_t
$$

### 1.3 Finite Distributed Lag Model (FDL)

Finite distributed lag model contains **lag terms** of independent variables:

$$
y_t = \alpha_0 + \delta_0 z_t + \delta_1 z_{t-1} + \cdots + \delta_q z_{t-q} + u_t
$$

Coefficient interpretations:

- **Impact propensity interpretation:** Assume that z increases by 1 unit only at time t and reverts to its previous level at time t+1 (i.e. the increase in z is temporary), then it is easy to show that $y_{t} - y_{t-1} = \delta_0$, $y_{t+1} - y_{t-1} = \delta_1$, $y_{t+2} - y_{t-1} = \delta_2$… We combine all the coefficients $(\delta_0, \delta_1,\cdots, \delta_q)$ and call it the **lag distribution(滞后分布)**.
- **Long-run propensity interpretation:** Assume that z permanently increases by 1 unit at and after time t, then it is easy to show that $y_{t} - y_{t-1} = \delta_0$, $y_{t+1} - y_{t-1} = \delta_0 + \delta_1$, $y_{t+2} - y_{t-1} = \delta_0 + \delta_1+\delta_2$… After a sufficiently long period of time, the change in y will reach $\sum\delta$ and remain constant, which is defined as the long-run propensity(LRP). It can be regarded as the “final result” of the cumulative effect of the lag distribution.

Remarks:

- **Multicollinearity** problem: $z_t \sim z_{t-1} \sim z_{t-2} \sim ...$ Hence, it is hard to get precise estimates of the individual δ.

## 2. Classical Assumptions

> i.e. Finite Sample Properties of OLS estimators

### 2.1 Assumptions for Unbiasedness

**TS1: Linear in Parameters**

The stochastic process $\{(\mathbf{x}_t, y_t)\}$ follows the linear model (t=1~n):

$$
y_t = \beta_0 + \beta_1x_{t1} + \cdots + \beta_kx_{tk} + u_t
$$

Remarks: $x_{tj}$ can be a lag term of an independent or dependent variable, such as $y_{t-1}$ or $y_{t-2}$…

**TS2: No Perfect Collinearity**

In the time series process, no independent variable remains constant nor a perfect linear combination of the others.

**TS3: Zero Conditional Mean (i.e. Strict Exogeneity)**

$$
E(u_t | \mathbf{X}) = 0,\quad t=1,...,n
$$

It implies both contemporaneous exogeneity:

$$
E(u_t|\mathbf{x}_t) = 0\ \text{for}\ \forall t
$$

and non-contemporaneous exogeneity:

$$
E(u_t|\mathbf{x}_s) = 0\ \text{for}\ \forall t\ne s
$$

**Remarks:**

- **[TS3]** First, non-contemporaneous exogeneity means $u_t$ cannot be correlated with past $\mathbf{x}$. This can be ensured by introducing the lag term of x into the model and making the assumption about contemporaneous exogeneity.
- **[TS3]** Second, non-contemporaneous exogeneity means $u_t$ cannot be correlated with future $\mathbf{x}$, which is tricky because in some cases, $y_t$ may likely have feedback on future x, meaning x will react to past y. For example, y refers to the monthly urban homicide count, and x equals the monthly police count. If the city adjusts current policing based on past crime rates, which sounds quite reasonable, then it certainly violates non-contemporaneous exogeneity.
- **[TS3]** It is easy to show that **AR(1) violates the non-contemporaneous exogeneity**.
  - $Cov(u_t, y_t) = Var(u_t) = \sigma^2$
- Why is there no assumption about independent sampling?
  - It cannot be true in time-series settings.
  - It is partially compensated by the non-contemporaneous exogeneity.
- **TS1 + TS2 + TS3 → OLS estimators are unbiased.**

### 2.2 Additional Assumptions for BLUE

**TS4: Homoskedasticity**

$$
Var(u_t|\mathbf{X}) = Var(u_t) = \sigma^2
$$

**TS5: No Serial Correlation**

$$
Cov(u_t,u_s|\mathbf{X}) = 0\ \text{for}\ \forall t\ne s
$$

**Remarks:**

- If TS5 is violated, we say that the error term exhibits serial correlation, also known as autocorrelation, or an autocorrelated error term.
- **TS1 + TS2 + TS3 + TS4 + TS5 (i.e. “Gauss-Markov assumptions”) → OLS estimators are BLUE.**
  - → We can easily compute the variance of OLS estimators;
  - → We can get the unbiased estimation for σ2…

### 2.3 Additional Assumptions for MVUE

**TS6: Normality**

$$
u_t \perp \mathbf{X}\\
u_t \sim_{i.i.d.} N(0, \sigma^2)
$$

**Remarks:**

- TS6 > TS3 + TS4 + TS5. Hence, it is a pretty strong assumption.
- **TS1 + TS2 + TS3 + TS4 + TS5 + TS6 (i.e. “Classical-Markov assumptions”)→ OLS estimators are MVUE.**
  - → We can do all the test and inference safely.

### 2.4 Other Topic: Trend and Seasonality

- Some common time trends:
  - Linear time trend: $y_t = \beta_0 + \beta_1t + u_t$
  - Quadratic time trend: $y_t = \beta_0 + \beta_1t + \beta_2t^2 +  u_t$
  - Exponential time trend: $log(y_t) = \beta_0 + \beta_1t + u_t$
- Why should trend terms be added? To avoid omitted variable bias - especially when independent variables are also highly trending. An alternative approach is to do regression after detrending IVs and DVs.
- **Seasonality**: If a time series is observed at monthly or quarterly intervals (or even weekly or daily), it may exhibit seasonality.
- We can incorporate seasonal dummies into the model to capture seasonality. For example, when working with monthly data, we can add 11 dummies for February to December, with January as the baseline. Additionally, we can conduct a joint F-test to determine whether there is significant seasonality (H0: all coefficients before month dummies are 0).
- Similarly, we can first deseasonalize independent and dependent variables, and then conduct regression using the deseasonalized data without incorporating seasonal dummies.

## 3. Modern Assumptions

### 3.1 Prerequisites: Stationarity and Weak Dependence

**Definition of _Stationarity_**: The stochastic process $\{x_t\}$ is stationary if for every collection of time indices $1 \le t_1 < t_2 < \cdots <t_m$, the joint distribution of $(x_{t_1}, x_{t_2}, \cdots, x_{t_m})$ is the same as the joint distribution of $(x_{t_1+h}, x_{t_2+h}, \cdots, x_{t_m+h})$ for all intergers $h \ge 1$.

- i.e. The distribution is identical everywhere for every combination.
- A weak version / A necessary condition - **Covariance Stationary:**

$$
E(x_t) = constant\\
Var(x_t) = constant\\
Cov(x_t, x_{t+h}) = f(h),\ \forall t,h \ge1
$$

**Definition of _Weak Dependence_**: The stochastic process $\{x_t\}$ is weakly dependent if $x_t$ and $x_{t+h}$ are “almost independent” as h increases without bound.

- i.e. The future cannot be greatly affected by the present.
- A weak version / A necessary condition - **Asymptotically uncorrelated:**

$$
Cov(x_t, x_{t+h}) \rightarrow 0\ \text{as}\ h \rightarrow \infin
$$

Remarks:

- **Why introduce stationarity and weak dependence?** To replace the assumption of random sampling in cross-sectional regression, ensuring LLN and CLT hold again, which are important for justifying OLS.
- Both **MA(1)** and **AR(1)** are both covariance stationary and weakly dependent (easy to prove).
  - Remember, AR(1) violates non-contemporaneous exogeneity. Hence, AR(1) violates TS3 but satisfies TS3’, which we will talk about later.
- **Random walk** is neither stationary nor weakly dependent.
  - In fact, all **unit root processes** are persistent (i.e. not weakly dependent), implying that current y is highly correlated with future y over a long span of time.
  - So, what should we do when dealing with highly persistent sequences, such as unit root process like random walk? **Do data transformation**. Weakly dependent processes are called **integrated of order zero**, which we do not need to do anything. However, unit root processes are called **integrated of order one** and require **first-order differencing**, resulting in the sequence becoming weakly dependent (and usually stationary as well).
- **How can we determine whether a sequence is weakly dependent?** Estimate an AR(1) model and test the coefficient of the lag term. If |ρ|<1, then it is I(0); if ρ=1, then it is I(1) and needs to be first-order differentiated.

### 3.2 Assumptions for Consistency

**TS1’: Linearity + Stationary & Weak Dependence**

- = TS1 + two prerequisites for the process $\{(\mathbf{x}_t, y_t)\}$

**TS2’: No Perfect Collinearity**

- The same as TS2

**TS3’: Zero Conditional Mean**

- Only contemporaneously exogenous: $E(u_t | \mathbf{x}_t) = 0$
- In this case, AR(1) satisfies TS3’.

**TS1’ + TS2’ + TS3’ → OLS estimators are consistent.**

- Not guarantee unbiasedness

### 3.3 Additional Assumptions for Asymptotic Normality

**TS4’: Homoskedasticity**

- Only contemporaneous as condition: $Var(u_t|\mathbf{x}_t) = Var(u_t) = \sigma^2$

**TS5’: No Serial Correlation**

- Only contemporaneous as condition: $Cov(u_t,u_s|\mathbf{x}_t, \mathbf{x}_s) = 0\ \text{for}\ \forall t\ne s$

**TS1’ + TS2’ + TS3’ + TS4’ + TS5’ → OLS estimators are asymptotically normally distributed.**

- The usual OLS se, t statistics, F statistics are asymptotically valid.
- Why is the assumption of normality (TS6’) not needed? Because it is a large-sample analysis.

## 4. Serial Correlation

> Serial correlation indicates a violation of TS5 or TS5’, which might be the most common problem in time series analysis. This section discusses the potential consequences of this violation, how to test for serial correlation, and how to address this problem.

### 4.1 Consequence

In the context of classical assumptions, serial correlation does not affect **unbiasedness** as long as the model satisfies TS1 to TS3. Similarly, under modern assumptions, serial correlation does not affect **consistency** as long as the model meets assumptions TS1’ to TS3’.

However, it does make the **standard errors** of OLS estimates and other test statistics invalid, thereby rendering various statistical tests unreliable. As for **R-squared**, if time series data is stationary and weakly dependent, even in the presence of serial correlation, R-squared remains useful.

A common **misconception** is that the *OLS estimator is inconsistent when lagged dependent variables are present*❌. Below are two examples to illustrate that the OLS estimator of an AR model can be consistent or inconsistent.

**Example: AR(1) model with contemporaneous exogeneity**

$$
y_t = \beta_0 + \beta_1 y_{t-1} + u_t \\
E(u_t | y_{t-1}) = 0
$$

- The 2nd equation represents contemporaneous exogeneity (i.e. TS3’).
- This “constructed” model satisfies TS3’ → **OLS is consistent**.
- However, without further information, we do not know whether there will be serial correlation.

**Example: AR(1) model with AR(1) errors**

$$
y_t = \beta_0 + \beta_1 y_{t-1} + u_t \\
u_t = \rho u_{t-1} + e_t


$$

- $\{e_t\}$ is a white-noise sequence.
- This “serially correlated” model violates TS3’: $Cov(y_{t-1} , u_t) = \rho Cov(y_{t-1}, u_{t-1}) \ne 0$ → **OLS is inconsistent**.
- However, this model can be transformed into an AR(2) model with a white noise error term.

### 4.2 Testing

#### 1) Regressors are strictly exogenous

When independent variables are strictly exogenous, test AR(1) serial correlation:

$$
u_{t}=\rho u_{t-1}+e_{t}
$$

**T test**

- Run the OLS regression of $y_t$ on $\mathbf{x}_t$ and obtain the OLS residuals $\hat{u}_t$
- Run the regression of $\hat{u}_t$ on $\hat{u}_{t-1}$
- Test whether the coefficient before $\hat{u}_{t-1}$ is significant (H0: ρ=0)

**Durbin-Watson test**

- Run the OLS regression of $y_t$ on $\mathbf{x}_t$ and obtain the OLS residuals $\hat{u}_t$
- Compute DW statistic:
  $$
  DW = \frac{\sum_{t=2}^n (\hat{u}_t-\hat{u}_{t-1})^2}{\sum_{t=1}^n \hat{u}_t^2}
  $$
- Find $d_L$ and $d_U$ in DW significance table, according to sample size and the number of regression parameters (H0: ρ=0; H1: ρ>0):
  - $DW<d_L$：reject H0
  - $DW>d_U$：cannot reject H0
  - Otherwise：inconclusive

#### 2) Regressors are not strictly exogenous

However, when independent variables are not strictly exogenous (e.g. AR(1) model or other model with lagged DV as regressors), or we do not know anything about the strict exogeneity, we should use the following approach to test AR(1) serial correlation:

- Run the OLS regression of $y_t$ on $\mathbf{x}_t$ and obtain the OLS residuals $\hat{u}_t$
- Run the regression of $\hat{u}_t$ on $\mathbf{x}_t, \hat{u}_{t-1}$
- Test whether the coefficient before $\hat{u}_{t-1}$ is significant (H0: ρ=0)

Meanwhile, this approach is easily extended to higher orders of serial correlation:

- [Same]
- Run the regression of $\hat{u}_t$ on $\mathbf{x}_t, \hat{u}_{t-1}, \hat{u}_{t-2}, \cdots$
- Jointly test whether the coefficients before $\hat{u}_{t-1}, \hat{u}_{t-2}, \cdots$ are significant

### 4.3 Correction

#### 1) Regressors are strictly exogenous

$$
y_t = \beta_0 + \beta_1 x_{t1} + \beta_2 x_{t2} + \cdots + \beta_k x_{tk} + u_t\\
u_t=\rho u_{t-1}+e_{t}
$$

If we do weighted differencing:

$$
y_t-\rho y_{t-1} = (1-\rho)\beta_0 + \beta_1(x_{t1}-\rho x_{t-1,1})+ \cdots+e_t
$$

Hence, we could:

- Run the OLS regression of $y_t$ on $\mathbf{x}_t$ and obtain the OLS residuals $\hat{u}_t$ (size=n)
- Run the regression of $\hat{u}_t$ on $\hat{u}_{t-1}$ to get $\hat{\rho}$ (size=n-1)
- Data transformation: $y_t^* = y_t- \hat{\rho} y_{t-1}$, $x_{tk}^* = x_{tk}-\hat{\rho} x_{t-1,k}$
- Run the final regression with transformed data (size=n-1)

Remarks: This process is called the **Cochrane-Orcutt procedure**, which omits the first observation in the final estimation step. Another similar procedure, called **Prais-Winsten estimation**, includes the first observation in the last step. However, asymptotically, it makes no difference whether or not the first observation is used.

#### 2) Regressors are not strictly exogenous

In this case, we use OLS to get consistent estimations but use another program to compute corrected standard errors and other test statistics (i.e. serial correlation–robust standard error).

**Eg: Newey-West Standard Error (with parameter g)**

```r
library(sandwich)

# Covariance matrix
Sigma <- NeweyWest(model, lag=4)

# Compute NW standard error for spdlaw and beltlaw
se_spdlaw  <- sqrt(diag(Sigma))["spdlaw"]      # 0.02547 <- 0.02057
se_beltlaw <- sqrt(diag(Sigma))["beltlaw"]     # 0.03336 <- 0.02323
```

The parameter g controls how much serial correlation we are allowing in computing se.
