---
title: NAT一探究竟
date: 2019-04-10 13:39:04
tags: [NAT, socket, 代理, VPN]
categories: 计算机网络
---

## 前言

一直好奇socket在内网情况下，是如何和外网链接的。
如果都是私有ip还好理解，可以相互通信。但是公有ip都可以用，数据是怎么返回就成了一个迷。

由于最近需要抓取内网与外网交换的数据包，涉及到了内网ip与外网ip的转化，之前用的代理软件把ip给隐藏掉了，看不到是谁发过来的信息。需要自己编写一个代理软件，涉及到socket，顺便把NAT也探究一下。

## [NAT基本原理及应用](https://blog.csdn.net/u013597671/article/details/74275852)

标题是外部链接

![nat转换图](/uploads/natdemo.jpg)

## [P2P，UDP和TCP穿透NAT](https://blog.csdn.net/leisure512/article/details/4900191)

标题是外部链接

## 搭建NAT服务器

先记下，等有了两个网卡，在尝试。

[Linux服务器](https://www.cnblogs.com/wonux/p/5555953.html)

[Win服务器](https://jingyan.baidu.com/article/3c343ff7e962840d3679637b.html)
