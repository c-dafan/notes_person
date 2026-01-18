---
title: conda大坑
date: 2019-04-13 13:29:41
tags: [python, conda, 奇坑, pip, 模块管理]
categories: [python]
---

---
记录一个遇到的坑。是非常的坑的坑。 --conda的使用

---

## conda坑

### conda install

conda的强大，一直也听说过，只是没想到如此的强大。只是想安装一个包，把我的整个python环境全都给我替换了。

我只是想装一下gdcm，但是py37没有这个包，所以pip显示没有匹配到。本着尝试的想法，用conda install，结果把整个环境从python37升级到了py36，（没错是升级，conda是这么提醒的）。当我发现有问题时，非常的绝望，但是怂，只能静悄悄的看着进度条走了半个小时。

结果不错所料，完全不能用了。conda强大到让你绝望中，又有了希望。居然还可以回滚。见回滚使用。

## conda使用

### 安装

```bash
conda install -n your_env_name [package]
```

默认装在root下。奇坑，小心着用。

### 创建环境

```bash
conda create -n your_env_name python=X.X（2.7、3.6等)
```

### 回滚

```bash
conda list --revision
conda install --version N  
```

N为回退的版本。

[参考](https://www.jianshu.com/p/97d3fc248cbb)

## pip


清华：https://pypi.tuna.tsinghua.edu.cn/simple

阿里云：http://mirrors.aliyun.com/pypi/simple/

中国科技大学: https://pypi.mirrors.ustc.edu.cn/simple/

华中理工大学：http://pypi.hustunique.com/

山东理工大学：http://pypi.sdutlinux.org/ 

豆瓣：http://pypi.douban.com/simple/

以后还是使用pip