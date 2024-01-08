---
title: >
  Math | Common Decompositions in Linear Algebra
author: Jingwei Gao
pubDatetime: 2024-01-08
postSlug: la-decom
featured: false
draft: false
tags:
  - math
  - linear algebra
description: >
  A summary about some common decompositions in linear algebra
---

## Table of contents

## 1. LU Decomposition

其中L为m×m的下三角矩阵，U为m×n的上三角矩阵：

$$
A = LU
$$

- A不必为方阵；不是所有矩阵都可以如此分解（必须可以不用行交换化为REF）。
- U就是A的一个REF(row echelon form)，L是一系列初等矩阵乘积的逆。

## 2. QR Decomposition

其中A**一般情况下为**可逆矩阵，此时Q为正交矩阵，R为上三角方阵：

$$
A = QR
$$

- 可以使用Gram-Schmidt Process求可逆矩阵的QR分解。

![](/assets/la-decom.jpeg)

## 3. Eigenvalue Decomposition

其中P为可逆矩阵，D为对角矩阵：

$$
A = PDP^{-1}
$$

- A必须为方阵；不是所有方阵都可以如此分解（必须“可对角化”）。
- D的对角线值是特征值，P的列是特征向量构成的一个Rn的basis（假设A为n×n矩阵）。

## 4. Spectral Decomposition

其中P为正交矩阵，D为对角矩阵：

$$
A = PDP^T = \sum_{i=1}^n \lambda_i \vec{p_i} \vec{p_i}^T
$$

- 条件和eigenvalue decomposition完全一样。
- 在eigenvalue decomposition的基础上，通过Gram-Schmidt process将矩阵P的列化为一个orthonormal basis即可。

## 5. Singular Value Decomposition

其中U为m×m方阵，V为n×n方阵，$\Sigma$为m×n对角阵（对角线上前k个值大于0，其余等于0）：

$$
A = U\Sigma V^T
$$

- A不必为方阵；所有矩阵都可如此分解（最灵活的一种分解方式）。
- k为rank(A)。
