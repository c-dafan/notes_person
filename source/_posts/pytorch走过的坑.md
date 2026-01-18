---
title: pytorch走过的坑
date: 2019-10-06 16:09:36
tags: [deeplearning, pytorch, note]
categories: [pytorch]
---

记录一下学习pytorch遇到的两大坑

>* zero_grad

需要收到清空历史梯度


>* torch.nn.CrossEntropyLoss

不用加入激活层softmax，在运算时已经加入了，如果加入softmax用nnl损失