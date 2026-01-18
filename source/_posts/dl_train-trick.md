---
title: 训练技巧总结
date: 2019-05-15 10:05:19
tags: [deeplearn, trick, train]
categories: [deeplearn]
---

## 数据处理

* 数据归一化。
* 随机乱序，在最后抖动的时候，会很稳定
* 数据增强器，对于数据少的情况，尤为重要。 [图像数据增强器的使用](/05/15/dl_ImageDataGenerator)

## 模型

* 加Batch Normalization, 加Batch Normalization很管用，尤其是数据没有归一化的时候。
* dropout一定程度可以防止过拟合

## 优化函数

* 优化函数，学习率。会影响一点
* adam 好用简单，但是优化到后面就优化步长太小了
* sgd 大佬专用，很多论文中，建议用sgd，但是对于我（小白），会出现loss不下降情况
* 学习率lr 衰减策略
* 学习率小一点，学习慢一点

[优化函数总结](https://blog.csdn.net/jiachen0212/article/details/80086926)