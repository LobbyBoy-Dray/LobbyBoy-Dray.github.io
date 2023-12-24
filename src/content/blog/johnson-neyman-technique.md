---
title: >
  Method | Johnson-Neyman Technique
author: Jingwei Gao
pubDatetime: 2019-03-25T15:20:35+08
postSlug: johnson-neyman-technique
featured: false
draft: false
tags:
  - regression
description: The Johnson-Neyman Technique is a statistical method used to determine the range of values of a moderator variable for which the relationship between two variables (e.g. DV ~ IV) is statistically significant.
---

> The Johnson-Neyman Technique is a statistical method used to determine the range of values of a moderator variable for which the relationship between two variables (e.g. DV ~ IV) is statistically significant.

## Table of contents

## 1. Motivation

A moderation model with a moderator M:

$$
Y=\beta_0+\beta_1X+\beta_2M+\beta_3XM
$$

Reformulate:

$$
Y=(\beta_0+\beta_2M)+(\beta_1+\beta_3M)X
$$

From the equation, we know that when M changes, the relationship between X and Y (i.e. $\beta_1+\beta_3M$) changes accordingly. On the one hand, we hope to understand how the relationship between X and Y varies with changes in M, such as when the direction of the relationship begins to reverse. On the other hand, we want to know how the significance of the relationship between X and Y varies with changes in M. This is where the Johnson-Neyman technique comes into play.

## 2. Example

> [Reference: Johnson Neyman in R (2 detailed examples) (tidypython.com)](https://tidypython.com/johnson-neyman-in-r-2-detailed-examples/)

Model: mpg ~ hp × wt (wt is the moderator)

![jn-model-result](/assets/jn-model-result.png)

JN will provide the following figure:

![jn-figure](/assets/jn-figure.png)

Green area represents a significant relationship between IV and DV: (1) When wt < 3.69，the effect of hp on mpg is significantly negative; (2) When wt > 5.90，the effect of hp on mpg is significantly positive; (3) When 3.69 < wt < 5.90, the effect of hp on mpg is not significant.

## 3. Software

- SPSS: PROCESS
- R: [Package interactions](https://cran.r-project.org/web/packages/interactions/index.html)
