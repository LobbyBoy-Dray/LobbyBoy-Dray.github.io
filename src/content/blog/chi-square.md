---
title: Method | Chi-square Test
author: Jingwei Gao
pubDatetime: 2024-05-08
postSlug: chi-square
featured: false
draft: false
tags:
  - statistics
description: >
  Chi-square for distribution test, independence test, and trend analysis.
---

## Table of contents

## What is the chi-square test?

The **chi-square test(卡方检验)**, also known as the **test of goodness of fit(拟合优度检验)**, is used to determine whether a set of observed data conforms to a particular theoretical distribution.

More specifically, it can test whether a **categorical variable** follows a particular distribution (**distribution test**) or whether two categorical variables are independent (**independence test**).

Since the statistic used in the tests follow a **chi-square distribution** when the null hypothesis is true, it is called a chi-square test.

<img src="/assets/chi-square-1.png" width="500">

The variables involved in chi-square tests are typically categorical. However, when dealing with continuous variables, such as testing the “H0: data comes from a normal distribution,” the data can be discretized first and processed similarly to categorical variables.

## One variable - Distribution test

Use the sample $\{X_1, X_2, \cdots, X_n\}$ to test the hypothesis:

$$
H_0: P(X=a_i) = p_i,\ i=1, \cdots,k
$$

Test statistic:

$$
\begin{align*}
Z &= \sum_{i=1}^k \frac{(O_i - E_i)^2}{E_i} \\&= \sum_{i=1}^k\frac{(n_i-np_i)^2}{np_i}
\end{align*}
$$

$O_i$ represents the observed frequency of category i, while $E_i$ represents the expected frequency of category i under the H0. Hence, the larger the statistic Z, the easier it is to reject H0.

Furthermore, when H0 is true and the sample size n is large enough, Z follows a chi-square distribution with **k-1 degrees of freedom**. Based on that, we can calculate the p-value or threshold and make a conclusion accordingly.

## Two variables - Independence test

There are two categorical variables. Their ranges are:

$$
X = 1,2, \cdots, a\\
Y = 1,2, \cdots, b
$$

Our sample is shown in the following **contingency table(列联表)**:

<img src="/assets/chi-square-2.jpeg" width="500">

In this scenario, the null hypothesis is (i.e. no relationship):

$$
H_0: X \perp Y
$$

or equivalently represented as:

$$
H_0: P(X=i, Y=j) = P(X=i)P(Y=j),\quad \forall i,j
$$

First, estimate the marginal distributions:

$$
P(X=i) \approx \hat{u}_i = n_{i*}/n\\
P(Y=j) \approx \hat{v}_j = n_{*j}/n
$$

Test statistic:

$$
\begin{align*}
Z &= \sum_{i=1}^k \frac{(O_i - E_i)^2}{E_i} \\&= \sum_{i=1}^k\frac{(n_{ij}-n \hat{u}_i \hat{v}_j)^2}{n \hat{u}_i \hat{v}_j}
\end{align*}
$$

When H0 is true and the sample size n is large enough, Z follows a chi-square distribution with **(a-1)(b-1) degrees of freedom**. Based on that, we can calculate the p-value or threshold and make a conclusion accordingly.

## Chi-squared test for trend

In both of the cases mentioned above, we haven't specified whether the categorical variables involved are **nominal(名义变量)** or **ordinal(定序变量)**. When dealing with ordinal variables, using the chi-square test in aforementioned ways (also referred to as “**Pearson’s chi-squared tests**”) fails to leverage the information provided by their ordering:

> _In spite of its usefulness, there are conditions under which the use of Pearson’s chi-square, although appropriate, is not the optimum procedure. Such a situation occurs when the categories forming a table have a natural ordering._

There are several other tests that take into account the ordering information when dealing with ordinal variables, such as the **Cochran–Armitage test for trend** and the **Mantel-Haenszel linear-by-linear association test**.

- [Cochran–Armitage test](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/procstat/procstat_freq_details76.htm) → **binary × ordinal**: whether the positivity rate increases or decreases with the increasing levels of the ordinal variable (i.e. linear trend in the proportions). Here is the [R code and demo](https://search.r-project.org/CRAN/refmans/DescTools/html/CochranArmitageTest.html).
  - `library(DescTools)` → `CochranArmitageTest`
- [Mantel-Haenszel test](https://online.stat.psu.edu/stat504/lesson/4/4.4) → **ordinal × ordinal**: whether there is a linear correlation between the two ordinal variables (i.e. linear relationship). It is enabled by default in SPSS.
  - [source file](https://online.stat.psu.edu/public/stat504/lesson04/code/MantelHaenszel.R) → `MH.test` or `MH.test.mid`
  - `library(DescTools)` → `MHChisqTest`

## References

- 陈希孺(2009)：《概率论与数理统计》，第5.3节
- Agresti, A. (2019). *An introduction to categorical data analysis*. Wiley.
- [Detailed intro to chi-square test from a medical statistics perspective](https://bookdown.org/wxhyihuan/Notebook-of-medical-statistics-1605856202966/%E7%AC%AC%E4%B8%83%E7%AB%A0-chi2%E6%A3%80%E9%AA%8C.html#%E5%8D%A1%E6%96%B9%E6%A3%80%E9%AA%8C)
- [Chi-Squared Test for Association in SPSS](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwiojdXSnPaFAxXLma8BHfDdBbUQFnoECEEQAQ&url=https%3A%2F%2Fwww.sheffield.ac.uk%2Fmedia%2F30957%2Fdownload%3Fattachment&usg=AOvVaw1yDujw5pPM33EgU997XmM1&opi=89978449)
- [Chi-Square - Sociology 3112 - The University of utah](https://soc.utah.edu/sociology3112/chi-square.php)
- [About the use and misuse of chi-square](https://web.pdx.edu/~jbroach/654/readings/delucchi%201993.pdf)
- [Yates's correction for continuity - Wikipedia](https://en.wikipedia.org/wiki/Yates%27s_correction_for_continuity)
