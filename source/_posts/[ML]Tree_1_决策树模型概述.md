---
title: ML | 决策树模型概述
date: 2018-3-3 01:01:30
tags: [ML.RF]
categories: ML
mathjax: true
abbrlink: ML_Decision_tree
---

## 决策树模型

决策树模型（Decision Tree model）是一个模拟人类决策过程思想得模型。

以找对象为例，一个女孩的母亲要给她的女儿介绍一个男朋友，于是有了下面得对话：

<!-- more -->

> 女儿：多大年纪了？**（年龄）**
>
> 母亲：26
>
> 女儿：长的帅不帅？**（长相）**
>
> 母亲：挺帅的
>
> 女儿：收入高不？**（收入情况）**
>
> 母亲：不算很高，中等情况
>
> 女儿：是公务员不？**（是否公务员）**
>
> 母亲：是，在税务局上班
>
> 女儿：那好，我去见见**（结果）**

![找对象](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/ML/%E6%89%BE%E5%AF%B9%E8%B1%A1.png)

### 决策过程

决策树基于“树”结构进行决策、判断

- 每个“内部结点”对应于某个属性上的测试
- 每个分支对应于该测试的一种可能结果（即该属性的某个取指）
- 每个“叶结点”对应一个“预测结果”

### 学习过程

通过对训练样本的分析来确定“划分属性”（即内部结点所对应的属性）

### 预测过程

将测试示例从根节点开始，沿着划分属性所构成的“判定测试序列”下行，直到叶结点

## 决策树简史

第一个决策树算法：CLS(Concept Learning System)

> [E. B. Hunt, J. Marin, and P.T.Stone's book "Experiments in Induction" published by Academic Press in 1966]

使决策树受到关注、成为机器学习主流技术的算法：ID3(Iterative Dichotomiser 迭代二分器的简称)
> [J. R. Quinlan's paper in a book "Expert Systems in the Micro Electronic Age" edited by D. Michie,  published by Edinburgh University Press in 1979]

最常用的决策树算法： C4.5
> [J. R. Quinlan's book "C4.5:Programs for Machine Learning" published by Morgan Kaufmann in 1993]

可以用于回归任务的决策树算法：CART (Classification and Regression Tree)
> L. Breiman, J. H. Friedman, R. A. Olshen, and C. J. Stone's book "Classification and Regression Trees" published by Wadsworth in 1984 ]

基于决策树的最强大算法：**RF** (Random Forest)
[L. Breiman's MLJ'  01 paper "Random Forest "]

## 总结

### 决策树模型

基于树的结构进行决策

- 属性、测试、预测结构

训练过程

- 分析训练样本，确定划分属性

预测过程

- 沿着树结构根据属性进行下行判定

### 决策树简史

- CLS
- J. R. Quinlan 1979 ID3
- J. R. Quinlan 1993 C4.5
- L. Breiman 1984 CART
- L. Breiman 2001 Random Forest