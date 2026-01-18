---
title: easy_install--打脸python爱好者
date: 2019-05-13 07:59:23
tags: [python, 模块管理， pip, conda]
categories: [python]
---

### easy_install的使用

作为python爱好者，竟然不知道有easy_install 的存在，简直是打脸。习惯使用pip或者conda，从来没注意过easy_install的存在。

可能大家都遇到过一个同样的问题，pip更新失败。这是一个非常让人恶心的问题，曾一度因为pip更新失败重装python环境。

有easy_install 再也不用担心pip更新失败的问题了。

easy_install的使用：

> 安装包

```bash
easy_install pip(包名)
```

> 更新包

```bash
easy_install -U pip(包名)
```

> 指定版本

```bash
easy_install pip=1.19(=版本号)
```

### pip更新失败，解决方案

> easy_install

```bash
easy_install -U pip
```

> conda

```bash
conda update pip
```
