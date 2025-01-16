---
title: Statistics | Poisson Model
author: Jingwei Gao
pubDatetime: 2025-01-16
postSlug: poisson
featured: false
draft: false
tags:
  - statistics
description: >
  The Poisson distribution is often used to count the number of “successes” in scenarios involving a large number of trials, each with a small probability of success.
---

## Table of contents

## 1. The Basic

The pmf of a Poisson random variable $X \sim \text{Poi}(\lambda )$:

$$
P(X=k) = e^{-\lambda} \lambda^k /k!\quad (k=0,1,2,\cdots)
$$

where $\lambda > 0$ called the “rate” parameter. This pmf can be easily verified as valid by using the fact that $\frac{\lambda ^k}{k!}$ is the Taylor expansion of $e^\lambda$ at 0. Also, we have:

$$
E(X)= Var(X)=\lambda
$$

## 2. Application

The Poisson distribution is often used to count the number of “successes” in scenarios involving a large number of trials, each with a small probability of success. For example,

- The number of emails received in an hour
- The number of earthquakes happened in a year
- The number of accidents occurring at a intersection during a period of time.

“The Poisson Approximation”: Event $A_1, A_2, \cdots, A_n$ with $P(A_j) = p_j$, $n$ is large and $p_j$’s are all small; These events are independent or weakly dependent >>> Then, the number of events that occur is approximately a Poisson with $\lambda = \sum_{j=1}^n p_j$.

## 3. Related Distribution: The Binomial

Let $X \sim B(n, p)$. As $n \to \infty$ and $p \to 0$ such that $np$ remains constant (i.e., their rates of convergence are similar), the pmf of $X$ converges to the pmf of a Poisson distribution with parameter $\lambda = np$.

$$
\begin{aligned}
P(X=k) &= \binom{n}{k}p^k (1-p)^{n-k} \\
 &= \frac{n(n-1)\cdots(n-k+1)·\lambda^k}{k! n^k} (1-\frac{\lambda}{n})^{n}(1-\frac{\lambda}{n})^{-k} \\
&= \frac{\lambda^k}{k!} \lim_{n \to \infin}(1-\frac{\lambda}{n})^{n} \\
&= \frac{\lambda^k}{k!} e^{-\lambda}
\end{aligned}
$$

Summary: Binominal can converge to a Poisson in a certain way.

## 4. Poisson Regression

Poisson regression should be used **when DV is a count variable**, such as the number of times an event occurs in a given time period. Model specification:

$$
y_i \sim \text{Poi}(\lambda_i)\\
\ln(\lambda_i) = \beta^T\mathbf{x}_i
$$

- Model estimation: MLE
- Model explanation:
  - intercept $\beta_0$: When all IVs are 0, the mean of DV is $\exp(\beta_0)$.
  - slope $\beta_k$: When all other IVs are held constant, increasing $x_k$ by one will multiplies the mean of DV by $\exp(\beta_k)$.

## 5. Poisson Regression with Offset

**Special case:** when $y_i$’s represent the counts within the different time interval.

- For instance, $y_1$ represents the number of phone calls within 1 hour, $y_2$ represents the number of phone calls within 3 hours…

In this case, the data are like $(x_i, y_i, t_i)$, instead of the original $(x_i, y_i)$. We can still use the Poisson model with a slight modification as follows:

$$
y_i \sim \text{Poi}(\lambda_i t_i)\\
\ln(\lambda_i) = \beta^T\mathbf{x}_i
$$

Here, $\lambda_i$ represents the count per unit length of time and $t_i$ represents the number of time units. Then, we perform the following transformation:

$$
y_i \sim \text{Poi}(\lambda_i t_i)\\
\ln(\lambda_i t_i) = \beta^T\mathbf{x}_i + \ln(t_i)
$$

Let $\lambda'_i = \lambda_it_i$, and then we can treat it as a “standard Poisson regression + adjusted systematic linkage.” Here, $\ln(t_i)$ is referred to as the **offset**.

## 6. Example: Age-Grouped Cancer Incidence in Danish Cities

The `eba1977` dataset from the [**ISwR**](https://cran.r-project.org/web/packages/ISwR/index.html) package contains counts of incident lung cancer cases and population size in four neighbouring Danish cities by age group:

<img src="/assets/poisson/poisson-1.png" width="300">

In this dataset, “pop” (i.e. population) is an attribute regarding to interval. Thus, we should use the Poisson model with offset. In this case, $\lambda_i$ represents the “#cancer cases/per person”, which is exactly the probability of developing cancer.

```r
## Import data
data(eba1977)
cancer.data = eba1977

## Add the offset column
logpop = log(cancer.data[ ,3])
new.cancer.data = cbind(cancer.data, logpop)

## GLM model
model = glm(cases ~ city + age+ offset(logpop),
            family = poisson(link = "log"), data = new.cancer.data)
summary(model)
```

The summary report:

<img src="/assets/poisson/poisson-2.png" width="700">

Interpretation of some coefficients:

- `Intercept = -5.63 (***)`: The probability of developing cancer for a person aging 40-54 and living in Fredericia is $\exp(-5.63) = 0.36\%$
  .
- `cityKolding = -0.37 (*)`: For individuals in the same age group, the probability of developing cancer in the city of Kolding is exp(-0.37)=0.69 times that in the city of Fredericia.
- `age75+ = 1.42 (***)`: For individuals living in the same city, the probability of developing cancer for those aged 75+ is exp(1.42)=4.14 times that for those aged 40–54.

## 7. References

- Harvard University. (2013, April 30). _Lecture 11: The Poisson distribution | Statistics 110 [Video]_. YouTube. [https://www.youtube.com/watch?v=TD1N4hxqMzY&list=PL2SOU6wwxB0uwwH80KTQ6ht66KWxbzTIo&index=11](https://www.youtube.com/watch?v=TD1N4hxqMzY&list=PL2SOU6wwxB0uwwH80KTQ6ht66KWxbzTIo&index=11)
- NYCU OCW. (2016, October 28). _Lec 31 Poisson regression and log-linear model [Video]._ YouTube. [https://www.youtube.com/watch?v=eQejmHDjd4Y&list=PLj6E8qlqmkFu0cY9PfwoFq6SbuZ-M28JE&index=31](https://www.youtube.com/watch?v=eQejmHDjd4Y&list=PLj6E8qlqmkFu0cY9PfwoFq6SbuZ-M28JE&index=31)
