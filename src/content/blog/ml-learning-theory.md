---
title: Learning Theory for Machine Learning
author: Jingwei Gao
pubDatetime: 2019-05-25T21:28:11+08
postSlug: ml-learning-theory
featured: false
draft: true
tags:
  - ml
description: A quick reference manual for MySQL commands.
---

- Mohri, M., Rostamizadeh, A., & Talwalkar, A. (2018). Foundations of machine learning. MIT press.

## 1. Introduction

### 1.1 What is Machine Learning?

Machine learning can be broadly defined as computational methods using experience to improve performance or to make accurate predictions.

作者认为，机器学习本质是各种计算方法的集合；利用这些计算方法，我们可以将经验(experience)转化为性能的改善(performance improvement)和更加精确的预测能力。

这里，experience其实就是data，data的数量和质量都很重要。

Machine learning consists of designing efficient and accurate prediction algorithms. (ML的核心是设计高效和准确的算法)。

在一般的计算机领域，我们常用时间复杂度(time complexity)和空间复杂度(space complexity)衡量一个算法的好坏；在机器学习中，我们还将使用样本复杂度(sample complexity)衡量算法的优劣：evaluate the sample size required for the algorithm to learn a family of concepts(算法实现学习目标所需要的样本数量)。

### 1.2 What kind of problems can be tackled using machine learning?

机器学习的主要应用领域：

- 文本分类：Text or document classification
- 自然语言处理：Natural language processing
- 语音识别：Speech processing applications
- 图像识别：Computer vision applications
- 计算生物学：Computational biology applications
- 其他领域：others

### 1.3 Some standard learning tasks

标准的机器学习问题：

- 分类问题(Classification)：assigning a category to each item;
- 回归问题(Regression)：predicting a real value for each item;
- 排序问题(Ranking): learning to order items according to some criterion(例如，web search，即对于搜索结果的排序)；
- 聚类问题(Clustering)：partitioning a set of items into homogeneous subsets;
- 降维(Dimensionality reduction)和流形学习(manifold learning)：transforming an initial representation of items into a lower-dimensional representation while preserving some properties of the initial representation.

在这一节中，作者提出了两个对于机器学习十分关键的问题：

1. 哪些问题可以被学习，可学习的条件是什么？Which concept families can actually be learned, and under what conditions?
2. 学习的效果如何衡量，算法能够学习得多好？How well can these concepts be learned computationally?

### 1.4 Learning stages

机器学习中的一些术语(作者以垃圾邮件识别器的构建为例)：

- 样本(Examples)
- 特征(Features)
- 标签(Labels)
- 超参数(Hyperparameters)：不由学习算法决定的参数，是对算法的“输入”——Free parameters that are not determined by the learning algorithm, but rather specified as inputs to the learning algorithm.
- 训练数据(Training sample)
- 验证数据(Validation sample)
- 测试数据(Test sample)
- 损失函数(Loss function)：衡量预测值与真实值差异的函数——A function that measures the difference, or loss, between a predicted label and a true label.
- 假设集(Hypothesis set)：一个个映射构成的集合——mapping.

一般流程：

1. 将数据集随机(random)分为训练集、验证集、测试集；
2. 特征工程(associate relevant features to the examples)：十分重要，一些好的特征能够很大程度帮助机器学习的训练，而不好的特征可能对学习过程十分不利。特征选择的过程，很大程度上依赖于操作者对该领域知识的掌握(user’s prior knowledge about the learning task which in practice)；
3. 用算法$A$在训练集上训练模型，再用验证集进行参数调优，得到最好的模型；
4. 用测试集进行测试，报告模型的准确度。

### 1.5 Learning scenario

- **监督学习(Supervised learning)**：有标签数据；
- **无监督学习(Unsupervised learning)**：数据无标签；
- **半监督学习(Semi-supervised learning)**：其中，没有标签的数据更多的是为学习提供关于分布的一些信息，作为监督学习的有利补充——_The hope is that the distribution of unlabeled data accessible to the learner can help him achieve a better performance than in the supervised setting_；
- **转导推理(Transductive inference)**：通过观察训练集合中的数据，直接对特定输入进行预测的方法，例如KNN。区别于归纳推理(Induction Inference)，归纳推理是从训练集拟合出一个一般化的模型，再用这个一般化的模型对输入进行预测，而转导推理并没有模型学习这个步骤；
- **在线学习(On-line learning)**：与之前的学习场景相比，在线学习涉及多轮次的学习(multiple rounds)与训练测试的交替进行(intermixed)；在每一轮中，学习器获得一个没有标签的训练数据，根据模型做出预测，然后得到真值，最后根据真值计算新增的损失(incur a loss)，并针对性地更新模型；在线学习的目标是最小化整轮的累积损失(cumulative loss over all rounds)，并且在线学习不需要对分布进行任何假设(no distributional assumption)；
- **强化学习(Reinforcement learning)**：同在线学习一样，强化训练中训练和测试也是交互进行的；在强化学习中，学习器不断地与环境(environment)互动，并通过互动获得奖励(reward)或惩罚(penalty)；通过不断重复这个过程，学习器会学到一系列动作(action)，能够最大化奖励；但是学习器会面临一个困境(dilemma)，探索(exploration)还是开发(exploitation)？Since he must choose between exploring unknown actions to gain more information versus exploiting the information already collected.
- **主动学习(Active learning)**：学习器通过主动询问的方式获得训练数据(The learner adaptively or interactively collects training examples, typically by querying an oracle to request labels for new points)；主动学习的目标是达到和监督学习相当的性能，但是训练使用的标签数据更少。

### 1.6 Generalization

机器学习问题可以抽象成从假说集(hypothesis set)中选择一个**泛化能力**最好的假说(hypothesis)：_The problem is typically formulated as that of selecting a function out of a hypothesis set, that is a subset of the family of all functions._

显然，假说集的选择会对机器学习的效果产生影响。

如果选择了一个比较复杂的假说集，那么学习器可能就会选到一个与训练样本十分契合的假说(hypothesis)，甚至可以100%地正确拟合训练样本；相反地，使用较为简单的假说集，选出的假说在训练数据上有错误是在所难免的——_With a rich or complex hypothesis set, the learner may choose a function or predictor that is consistent with the training sample, that is one that commits no error on the training sample. With a less complex family, incurring some errors on the training sample may be unavoidable._

这里就有两个问题需要考虑：

1. 如何衡量假说集的复杂程度？
2. 到底应该选简单的假说集还是复杂的？

总体而言，对于训练数据拟合的过于完美的模型，往往不是最优模型，其泛化能力可能反而更差(_In general, the best predictor on the training sample may not be the best overall_)；因此，对于复杂的假说集我们应该谨慎使用，因为假说集越复杂，它的拟合能力越强，甚至能够完全“记住”训练数据(memorize the data)，从而在训练集上达到100%的正确率。

但话又说回来，假说集过于简单，也可能会导致泛化能力很差，这就是欠拟合(underfit)；上面的情况是过拟合(overfit)。

因此，我们需要根据训练样本的数量选择合适复杂度的假说集合：如果有非常多的训练样本，那么用稍微复杂一点的假说集合没有什么问题；但是样本很少时，用复杂的假说集合，就可能有过拟合的危险——_We will see that the trade-off between the sample size and complexity plays a critical role in generalization._

## Ch2: The PAC Learning Framework

对于机器学习，提出了几个概括性的问题：

- 什么样的问题能够被有效率的学习(_What can be learned efficiently_)？
- 什么样的问题天生无法有效地被学习(_What is inherently hard to learn_)？
- 成功的学习需要多少样本(_How many examples are needed to learn successfully_)？
- 学习有没有一个综合性的模型指导(_Is there a general model of learning_)？

PAC学习框架(Probably Approximately Correct learning framework)可以解释上述问题。

(_The PAC framework helps define the class of learnable concepts in terms of the number of sample points needed to achieve an approximate solution, sample complexity, and the time and space complexity of the learning algorithm, which depends on the cost of the computational representation of the concepts._)

### 2.1 The PAC learning model

首先，为了叙述PAC模型，我们需要引进一些记号：

- 输入空间(input space)记作$\mathcal{X}$，表示所有样本(examples)或实例(instances)的所有可能取值的集合；
- 所有可能的**标签(labels)**或**目标值(target values)**的集合记作$\mathcal{Y}$——在本章中，我们假设$\mathcal{Y}=\{0,1\}$，即**二元分类问题(binary classification)**；
- 一个**概念(concept)**记作$c: \mathcal{X}→\mathcal{Y}$，是从$\mathcal{X}$到$\mathcal{Y}$的一个映射(规则)；由于选择了二元分类问题，那么我们也可以将$c$等价地用$\mathcal{X}$的子集来表示，这些子集表示着取1的样本；
- 一个**概念类(concept class)**是一些我们希望学习到的概念的集合，记作$\mathcal{C}$；
- 我们假设，所有的样本都是**独立同分布的(i.i.d.)**，满足一个固定的但是未知的分布$\mathcal{D}$(fixed but unknown distribution)。

根据上述记号，我们可以将学习问题进行如下叙述：

1. 首先，给定**学习器(learner)**一个由可能概念构成的固定集合，即**假说集(hypothesis set)**，记作$\mathcal{H}$，这个假说集不一定必须和$\mathcal{C}​$有重合；
2. 接着，给予学习器样本$S = (x_1,\cdots,x_m)$与对应的标签$(c(x_1), \cdots, c(x_m))$，其中每个样本都是根据分布$\mathcal{D}$来i.i.d.得到的，$c$是需要学到的**目标概念**，属于概念类$\mathcal{C}$；
3. 学习器需要根据带有标签的数据集$S$，从假说集$\mathcal{H}$中选择一个假说$h_S$，该$h_S$有着相对于目标概念$c$很小的**泛化误差(generalization error)**。

泛化误差(generalization error)的定义：给定$h \in \mathcal{H}$，$c \in \mathcal{C}$，分布$\mathcal{D}$，$h$的泛化误差，或者叫做风险(risk)，定义为：

$$R(h) = \underset{x \sim \mathcal{D}}{P}[h(x) \ne c(x)]​$$

一个假说的泛化误差不能被直接求解，因为我们既不知道分布，也不知道概念。但是，我们可以计算一个假说在训练数据上的**经验误差(empirical error)**：

$$\hat{R}_S(h) = \frac{1}{m}\sum_{i=1}^m[h(x_i) \ne c(x_i)]$$

比较经验误差与泛化误差：

- 经验误差是$h$在训练数据$S$上的平均误差；
- 泛化误差是$h$在分布$\mathcal{D}$上的期望误差；
- 两者似乎有联系——因为$S$是根据$\mathcal{D}$独立同分布产生的：

$$\underset{S \sim \mathcal{D}^m}{E}[\hat{R}_S(h)] = R(h)$$

将数据$x$的计算复杂度记作$O(n)$；将算法$\mathcal{A}$通过训练数据$S$得到的模型输出记作$h_S$，即$h_S = \mathcal{A}(S)$(_Let $n$ be a number such that the computational cost of representing any element $x \in \mathcal{X}$ is at most $O(n)$ and denote by $size(c)$ the maximal cost of the computational representation of $c \in \mathcal{C}$_)。

下面，我们将定义什么是**PAC可学习的(PAC Learnable)**：

如果存在算法$\mathcal{A}$和多项式函数$poly(·,·,·,·)$，满足：对于任意$\epsilon > 0$和$\delta > 0$，(再)对于所有$X$的分布$\mathcal{D}$和任何目标概念$c \in \mathcal{C}$，以下不等式对于任何样本量$m \ge poly(1/\epsilon,1/\delta,n,size(c))$成立：

$$\underset{S \sim \mathcal{D}^m}{P}[R(h_S) \le \epsilon] \ge 1-\delta$$

<!-- ![Alt text](./微信截图_20190521182932.png) -->

因此，我们知道，一个概念类$\mathcal{C}$被称为PAC可学习的，意味着：算法在观测完一定数量(数量是$1/\epsilon$和$1/\delta$的多项式函数，指数型函数就不行)的样本后，返回的假设，“在很大程度上(_with high probability_，至少$1-\delta$)”是“近似正确的(_approximately right_。错误最多是$\epsilon$)”，所以叫做**PAC**。

**PAC的要点**：

1. PAC框架对分布$\mathcal{D}​$没有任何假设，仅假设其存在：_First, the PAC framework is a distribution-free model: no particular assumption is made about the distribution D from which examples are drawn_；
2. 训练样本和测试样本都是从同一分布中产生的：_Second, the training sample and the test examples used to define the error are drawn according to the same distribution D_；
3. PAC可学习是针对概念类谈的，而非特定的那个目标概念：_Finally, the PAC framework deals with the question of learnability for a concept class $\mathcal{C}$ and not a particular concept. Note that the concept class $\mathcal{C}$ is known to the algorithm, but of course the target concept $c \in \mathcal{C}$ is unknown_。

下面，我们将针对一些特定的学习问题，阐述PAC学习的思想。

**例1：沿轴矩形的学习(axis-aligned rectangles)**

考虑一个输入空间是整个二维平面的问题；概念类$\mathcal{C}​$是二维平面中所有沿轴矩形(各边与坐标轴平行的矩形)的集合(矩形内部是1，外部是0)。我们的学习问题是，根据一些带标签的训练样本，学习一个沿轴矩形，其泛化误差很小。我们将证明，沿轴矩形这样的概念类是PAC可学习的。

在下图中，$R$代表目标沿轴矩形，$R’$是一个假说。$R’$的错误区域由两个部分组成：

- false negatives：在$R$中但不在$R’$中的点；
- false positives：在$R’$但不在$R$中的点；

<!-- ![Alt text](./微信截图_20190521185144.png) -->

我们定义这样一个算法$\mathcal{A}​$：对于一个训练样本集$S​$，算法将返回一个包纳了所有正类的“最紧”的沿轴矩形(_the tightest axis-aligned rectangle containing the points labeled with 1_)，记作$R_S​$，如下图所示。易知，$R_S​$不包括任何false positive，其错误区域在$R​$内。

<!-- ![Alt text](./微信截图_20190521184728.png) -->

记$P[R]​$为目标概念$R​$的“区域概率(_the probability mass of the region defined by R_)”，也就是由分布$\mathcal{D}​$随机产生的一个点落在$R​$中的概率(_that is the probability that a point randomly drawn according to D falls within R_)；

固定$\epsilon > 0$，我们假设$P[R] > \epsilon$。这样假设的理由是，如果$P[R] \le \epsilon$，那么因为$R(R_S) \le P[R]$，所以无论$S$是什么一定有$R(R_S) \le \epsilon$，没有意义。因此假设$P[R] > \epsilon$。

接下来我们将构造四个**沿着矩形边的小矩形(沿边矩形)**$r_1,r_2,r_3,r_4$，要求面积最小，但需要满足$\underset{\mathcal{D}}{Pr}[r_i] \ge \frac{\epsilon}{4}$(_Let be four smallest rectangles along the sides of $R$ such that $\underset{\mathcal{D}}{Pr}[r_i] \ge \frac{\epsilon}{4}$_)。

<!-- ![Alt text](./微信截图_20190521225617.png) -->

如果我们将$R$表示为$[l,r]×[b,t]$(left, right, bottom, top)，我们也可以将$r_4$表示为$[l,s_4]×[b,t]$，其中$s_4 = inf\{s: Pr[[l,s]×[b,t]] \ge \frac{\epsilon}{4}\}$

$R_S$与四个沿边矩形$r_i$都有交集，能推出$R-R_S$，即$R(R_S)$的区域，一定小于等于$4×\frac{\epsilon}{4}=\epsilon$。

其**逆否命题**是，若$R(R_S)>\epsilon$，则$R_S$与四个沿边矩形$r_i$至少有一个没有没有交集：

$$Pr[R(R_S)>\epsilon] \le Pr[\cup^4_{i=1} \{R_S\ misses\ r_i\}]$$

由Union Bound得到：

$$Pr[R(R_S)>\epsilon] \le Pr[\cup^4_{i=1} \{R_S\ misses\ r_i\}] \le \sum_{i=1}^4 Pr[\{R_S\ misses\ r_i\}]$$

现在来简单分析$Pr[\{R_S\ misses\ r_i\}]$。此概率表示算法挑到的假设所对应的区域与一个沿边矩形无交集，则必须没有样本点在沿边矩形上或内部。由沿边矩形的区域概率为$\frac{\epsilon}{4}$知，任何一点落在沿边矩形外部的概率为$1-\frac{\epsilon}{4}$，所有m个样本点都落在沿边矩形外部的概率为$(1-\frac{\epsilon}{4})^m$，因此$Pr[\{R_S\ misses\ r_i\}] = (1-\frac{\epsilon}{4})^m$。

所以有：

$$Pr[R(R_S)>\epsilon] \le 4 (1-\frac{\epsilon}{4})^m$$

由$1-x \le e^{-x}$得：

$$Pr[R(R_S)>\epsilon] \le 4exp(-m\epsilon/4)$$

因此，对任意$\delta > 0$，要使：

$$Pr[R(R_S)>\epsilon] \le \delta$$

必须有：

$$4exp(-m\epsilon/4) \le \delta$$

即：

$$m \ge \frac{4}{\epsilon}log\frac{4}{\delta}$$

通过上述推导，我们知道，对于任意的$\epsilon>0$和$\delta>0$，如果样本容量$m \ge \frac{4}{\epsilon}log\frac{4}{\delta}$，则有PAC——$Pr[R(R_S)>\epsilon] \le \delta$。因此，**沿轴矩形是PAC可学习的**，其样本复杂度(sample complexity)是$O( \frac{1}{\epsilon}log\frac{1}{\delta})$。

另一种表示PAC可学习的方式是使用**泛化边界(generalization bound)**。泛化边界是说，至少有$1-\delta​$的概率，算法输出假设$h_S​$的泛化误差$R(R_S)​$的上界(upper bound)被某个依赖于样本容量$m​$与$\delta​$的数值限定住。

对于沿轴矩形的学习问题，之前已经证明有$Pr[R(R_S)>\epsilon] \le 4exp(-m\epsilon/4)$。令$\delta = 4exp(-m\epsilon/4)$，得$\epsilon = \frac{4}{m}log\frac{4}{\delta}$。则用泛化边界的语言叙述，PAC为：至少有$1-\delta$的概率，泛化误差会被限定在$\frac{4}{m}log\frac{4}{\delta}$之内。

对于上述沿轴矩阵问题，尽管假说集合是无限的，但是我们巧妙地利用了问题本身的特殊性——沿边矩形的一些几何特性，问题被轻松解决。但正由于我们利用了问题本身的特殊性，因此该问题的解决对于其他PAC证明问题不具备参考意义。我们还需要更加一般的方法。

## 2.2 有限假说集的保证：一致的情况

> Guarantees for finite hypothesis sets — consistent case

> 本书与周志华老师的《机器学习》，都以目标概念$c$是否在假说集合$\mathcal{H}$中为依据，将有限假说集合的学习问题划分为两大类：一致性情形，即$c \in \mathcal{H}$；不一致情形，$c \notin \mathcal{H}$。然而，根据两本书的论证过程，似乎一致情形是指，存在$h \in \mathcal{H}$，$h$的经验误差为0；不一致情形是指，不存在$h \in \mathcal{H}$，$h$的经验误差为0。因为，如果按照原书的定义，在不一致情形下，即使$c \notin \mathcal{H}$，也可能存在$h \in \mathcal{H}$，其经验误差为0(但泛化误差可能很大)。

**一致情形(consistent)**：$c \in \mathcal{H}$。必要条件——存在$h \in \mathcal{H}$，其经验误差为0(特别注意，不是充分条件)。

**(定理：一致情形，有限假说集合)**令$\mathcal{H}$为有限的假说集合，对于任意的目标概念$c \in \mathcal{H}$，算法$\mathcal{A}$接受i.i.d.的训练样本$S$，将返回一个一致的假说$h_S: \hat{R}_S(h_S)=0$。那么，对于任意的$\epsilon, \delta > 0$，当：

$$m \ge \frac{1}{\epsilon}\Big(log|\mathcal{H}| + log \frac{1}{\delta}\Big)$$

有：

$$P_{S \sim \mathcal{D}^m}[R(h_S) \le \epsilon] \ge 1-\delta$$

用泛化边界的思路叙述为：对于任何$\epsilon, \delta >0$，有至少$1-\delta$的概率下式成立：

$$R(h_S) \le \frac{1}{m}\Big(log|\mathcal{H}| + log \frac{1}{\delta}\Big)$$

**证明**：

固定$\epsilon > 0​$。我们将有限假说集合$\mathcal{H}​$中泛化误差大于$\epsilon​$的假说再集合起来，记作$\mathcal{H}_{\epsilon} = \{h \in \mathcal{H}: R(h) > \epsilon\}​$。我们来研究这个集合$\mathcal{H}_{\epsilon}​$。其中，可能存在这样的假说，它们的经验误差是0，概率是：

$$P\Big[\exists h \in \mathcal{H}_{\epsilon}: \hat{R}_S(h) = 0\Big] = P\Big[\hat{R}_S(h_1) = 0 \bigcap \cdots \bigcap  \hat{R}_S(h_{|\mathcal{H}_{\epsilon}|}) = 0\Big] \underset{union\ bound}{\le} \sum_{h \in \mathcal{H}_{\epsilon}} P\Big[\hat{R}_S(h) = 0\Big]$$

因为$\mathcal{H}_{\epsilon}$中的假说泛化误差都大于$\epsilon$，泛化误差的含义是对随机一个样本，预测错误的概率。因此，任一$\mathcal{H}_{\epsilon}$中的假说，其经验误差为0的概率为：

$$P\Big[\hat{R}_S(h) = 0, h \in  \mathcal{H}_{\epsilon}\Big] = (1-\epsilon)^m$$

所以：

$$P\Big[\exists h \in \mathcal{H}_{\epsilon}: \hat{R}_S(h) = 0\Big] \le |\mathcal{H}|(1-\epsilon)^m \le |\mathcal{H}|e^{-m\epsilon}$$

我们并不知道算法$\mathcal{A}$将会输出哪一个一致性的假说，但我们知道(事件的包含关系，左边事情发生，则右边事情一定发生)：

$$P\Big[R(h_S) > \epsilon\Big] \le P\Big[\exists h \in \mathcal{H}_{\epsilon}: \hat{R}_S(h) = 0\Big]$$

所以有：

$$P\Big[R(h_S) > \epsilon\Big] \le |\mathcal{H}|e^{-m\epsilon} = \delta$$

故至少有$1-\delta$的概率使得下式成立：

$$R(h_S) \le \frac{1}{m}\Big(log|\mathcal{H}| + log \frac{1}{\delta}\Big)$$

**举例：布尔变量的连接(Conjunction of Boolean literals)**

概念类$\mathcal{C}_n$表示最多$n$个布尔变量(Boolean literals)$x_1,\cdots,x_n$的合取(conjunctions，可以简单理解为**逻辑且**)。一个布尔变量是原来的$x_i$，或者它的“反(negation)”$\overline{x_i}$。例如，$n=4$，一个合取是$x_1 \wedge \overline{x_2} \wedge x_4$，其中$\overline{x_2}$就表示原布尔变量$x_2$的“反”。对于这个合取概念来说，$(1,0,0,1)$是正类，$(1,0,0,0)$是负类。

<!-- ![Alt text](./微信截图_20190522161803.png) -->

对于$n=4​$，如果存在一个正类样本$(1,0,1,0)​$，那么我们可以推断，目标概念肯定不含有$\overline{x_1}​$或$\overline{x_3}​$，也肯定不含有$x_2​$和$x_4​$。相反的，一个负类样本则不会给我们多少有用的信息。

我们设计这样一个算法：对于每一个正类样本$(b_1,b_2,\cdots,b_n)$，如果$b_i=1$则将$\overline{x_i}$排除，如果$b_i=0$则将$x_i$排除。最后，将所有没有被排除的进行合取即可。

我们的假说集合$\mathcal{H}$的容量$|\mathcal{H}| = 3^n$，因为每一个位置都由三种情况：不包括、原来的、相反的。且本问题的$\mathcal{H}$就是概念类，所以是一致的。运用上面的定理，我们知道，需要的样本量为：

$$m \ge \frac{1}{\epsilon}\Big((log3)n + log \frac{1}{\delta}\Big)​$$

例如，$\delta=0.02, \epsilon=0.1, n=10$，边界变成$m \ge 149$。即，当样本量大于等于149时，就能够有98%的概率保证，算法拿到的假说的泛化误差小于0.1(精确度为90%)。

## 2.3 有限假说集的保证：不一致的情况

> Guarantees for finite hypothesis sets — inconsistent case
>
> 个人想法：一致情形并不代表$c \in \mathcal{H}$，而是代表存在$h_i \in \mathcal{H}$，其$\hat{R}_S(h_i)=0$。不一致情形不仅仅代表$c \notin \mathcal{H}$，而且代表不存在$h_i \in H$，其$\hat{R}_S(h_i)=0$。

然而，大多数情况下，我们的假说集合都是不一致的。充分条件——不存在$h \in \mathcal{H}$，其经验误差为0(特别注意，不是必要条件)。这个时候，我们希望算法选到的假说，其经验误差和泛化误差十分接近，这样我们就可以安全地用经验误差来代替泛化误差进行选择了——因为泛化误差和经验误差很近，且经验误差很小，所以算法输出的假说泛化误差也很小！

这里的核心是**Hoeffding’s不等式**，该不等式能够将经验误差和泛化误差联系起来(_we will use Hoeffding’s inequality or the following corollary, which relates the generalization error and empirical error of a single hypothesis_)。

Hoeffding’s不等式(的推论)：对于二元分类问题，任何一个固定的假说$h$(a fixed hypothesis)

$$P\Big[|\hat{R}_S(h)-R(h)| \ge \epsilon\Big] \le 2exp(-2m\epsilon^2)$$

**(定理：不一致情形，有限假说集合)**令$H$为有限的假说集合。对任何$\delta > 0$，至少有$1-\delta$的概率使下式成立：

$$\forall h \in H,\quad R(h) \le \hat{R}_S(h) + \sqrt{\frac{log|\mathcal{H}|+log \frac{2}{\delta}}{2m}}$$

也就是(这是自己加的，说明算法的选择是“安全的”)：

$$R(h_S) \le \hat{R}_S(h_S) + \sqrt{\frac{log|\mathcal{H}|+log \frac{2}{\delta}}{2m}}$$

**证明**：

$$
\begin{align*}
 P\Big[\exists h \in \mathcal{H}\ |\ |\hat{R}_S(h)-R(h)| \ge \epsilon\Big]  &= P\Big[( |\hat{R}_S(h_1)-R(h_1)| \ge \epsilon) \bigcap \cdots \bigcap ( |\hat{R}_S(h_{|\mathcal{H}|})-R(h_{|\mathcal{H}|})| \ge \epsilon)\Big] \\
 & \underset{union\ bound}{\le} \sum P\Big[|\hat{R}_S(h)-R(h)| \ge \epsilon\Big]\\
 & \le 2|\mathcal{H}|exp(-2m\epsilon^2)
\end{align*}​
$$

令$\delta = 2|\mathcal{H}|exp(-2m\epsilon^2)​$，证毕。

用Hoeffding’s导出的这个bound当然也适用于一致情形，因为这是比一致情形下推出的bound更加松一点的bound。从泛化边界中我们可以知道，样本容量的扩大是非常有益的，假说集合的扩大则不利于泛化，但在有限集合的情况下，总是可以用大的样本容量来弥补的(_a larger sample size m guarantees better generalization, and the bound increases with |H|, but only logarithmically_)。

从泛化边界中我们也能看到一种权衡取舍(trade-off)：减小算法输出假设的经验误差(reducing the empirical error) vs. 控制假说集合的大小(controlling the size of the hypothesis set)——大的假说集合，算法能够取到更小经验误差的假说，但是其泛化能力可能不强。

因此，最好遵从奥卡姆剃刀原则(Occam’s Razor principle)，尽量使用简单的假说集合(_All other things being equal, a simpler (smaller) hypothesis set is better_)。

## 2.4 其他

> Generalities
>
> 本节配合林轩田老师机器学习基石的第8讲食用，效果更好

**确定情景与随机情景(Deterministic versus stochastic scenarios)**

考虑监督学习。在大多数情况下，分布$\mathcal{D}$是输入和输出空间的联合分布，即$P(x,y)$。随机情景的意思就是，输出是输入的一个概率分布函数，而不是一个确定的值(_Within this setting, the output label is a probabilistic function of the input_)。意思就是，hypothesis是条件概率模型，$P(Y|X)$。

我们之前都是在用确定情景进行讨论。确定情景是指，输出是输入的一个确定的值(_When the label of a point can be uniquely determined by some measurable function $f : X → Y$ (with probability one), then the scenario is said to be deterministic_)。意思就是，hypothesis是决策函数模型，$f(X)$，当然也包括$argmaxP(Y|X)$这样的。

需要强调书中所提的measurable function，用$f$表示，应该就是决策函数。

**贝叶斯误差和噪声(Bayes error and noise)**

在确定情景下，我们认为，那个需要学习的目标概念，是“目标函数(target function)”，其泛化误差当然是0。但在随机情景下，我们认为，那个需要学习的目标概念，是“目标分布(target distribution)”，而不存在哪一个假说其泛化误差能够达到0(_In the stochastic case, there is a minimal non-zero error for any hypothesis_)。

比如，考虑这样一个随机情景。长头发的人，99%为女性，1%为男性。这样，无论你预测是男性还是女性，都会有一个“对立”的可能状态存在，因此不能说预测的误差是0。而确定情景是，长头发的人是女性。这样，预测是可以达到误差为0。

**(定义：贝叶斯误差)**：给定输入空间与输出空间的联合分布$\mathcal{D}$，贝叶斯误差$R^*$被定义为决策型函数泛化误差的下确界(Given a distribution $\mathcal{D}$ over $\mathcal{X} × \mathcal{Y}$, the Bayes error $R^∗$ is defined as the infimum of the errors achieved by measurable functions $h: \mathcal{X} → \mathcal{Y}$)：

$$R^* = \underset{h,\ h\ measurable}{inf}R(h)$$

A hypothesis $h$ with $R(h) = R^∗$ is called a Bayes hypothesis or Bayes classifier.

可见，贝叶斯分类器可以被定义为条件概率样式：

$$\forall x \in \mathcal{X},\quad h_{Bayes}(x) = \underset{y \in \{0,1\}}{argmax}P[y|x]$$

**(定义：噪声)**：给定输入空间与输出空间的联合分布$\mathcal{D}$，输入空间中一个点$x$的噪声被定义为(Given a distribution $\mathcal{D}$ over $\mathcal{X} × \mathcal{Y}$, the noise at point $x \in \mathcal{X}$ is defined by)

$$noise(x) = min\Big\{P(1|x),P(0|x) \Big\}$$

关于$\mathcal{D}$的平均噪声为$E[noise(x)] = R^*$。
