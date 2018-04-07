---
title: "Google机器学习笔记"
date: 2018-03-25T21:41:51+08:00
lastmod: 2018-03-25T21:41:51+08:00
draft: false
keywords: ["机器学习"]
description: "Google机器学习笔记"
tags: []
categories: ["machine-learning"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: true
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: true
mathjaxEnableSingleDollar: true
---

最近正在抽空学习Google的[《机器学习速成课程》](https://developers.google.com/machine-learning/crash-course)。由于拖延症晚期加上工作忙碌，学习进度缓慢，经常遇到这次看的内容下次就忘了的情况，所以在此记录一下，以加强记忆，巩固所学知识。
<!--more-->

监督式机器学习定义：机器学习系统通过学习如何组合输入信息来对从未见过的数据做出有用的预测。

# 标签

我们要预测的事物，如小麦未来的价格、图片中显示的动物的品种等，在简单线性回归（{{< pure "$y=b+{ \omega  }_{ 1 }{ x }_{ 1 }+{ \omega  }_{ 2 }{ x }_{ 2 }$" >}}）中的`y`

# 特征

输入变量，在垃圾邮件检测器示例中，特征可能包含：

- 电子邮件文本中的字词
- 发件人的地址
- 发送电子邮件的时段

在简单线性回归中的`x`，对于复杂的机器学习项目，特征会有多个，表示为：

{{< pure "$$\left\{ { x }_{ 1 },{ x }_{ 2 },{ x }_{ 3 },...{ x }_{ n } \right\}$$" >}}

# 样本

## 有标签样本

同时具有特征和标签，用于训练模型。

## 无标签样本

只有特征没有标签，用来测试模型。

# 模型

表示特征与标签之间的关系。模型生命周期的两个阶段：

- 训练，表示创建或学习模型。向模型展示有标签样本，让模型学习特征与标签之间的关系。
- 推断，将训练后的模型应用于无标签样本。

# 回归与分类

回归模型预测连续值，分类模型预测离散值。

# 线性回归

对于具有三个特征的模型，方程式如下：

{{< pure "$${ y }^{ \prime  }=b+{ \omega  }_{ 1 }{ x }_{ 1 }+{ \omega  }_{ 2 }{ x }_{ 2 }+{ \omega  }_{ 3 }{ x }_{ 3 }$$" >}}

其中：

- {{< pure "${ y }^{ \prime  }$" >}}指的是预测的标签
- {{< pure "$b$" >}} 指偏差（与y轴的截距）
- {{< pure "${ \omega  }_{ 1 }$" >}}特征 1 的权重
- {{< pure "${ x }_{ 1 }$" >}}特征 1（输入项）

# 训练与损失

## 经验风险最小化

检查多个样本并尝试找出可最大限度减少损失的模型

## 损失

损失是一个**数值**，表示对于**单个**样本而言模型预测的准确程度。

## 损失函数

常见的损失函数：平方损失（L<sub>2</sub>），单个样本的平方损失如下：

= (observation - prediction(x))<sup>2</sup>

= (y - y')<sup>2</sup>

即实际值与预测值差值的平方

均方误差（MSE）指的是每个样本的平均平方损失。计算公式为：

{{< pure "$$MSE=\frac { 1 }{ N } \sum _{ (x,y)\in D }^{  }{ (y-prediction(x))^{ 2 } }$$" >}}
