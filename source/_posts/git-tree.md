---
title: git_tree
date: 2019-09-24 18:08:43
tags: [git, trick]
categories: [git]
---


DownGit链接：https://www.itsvse.com/downgit/


现在有一个test仓库https://github.com/mygithub/test
你要gitclone里面的tt子目录：
在本地的硬盘位置打开Git Bash

```
git init test && cd test     //新建仓库并进入文件夹
git config core.sparsecheckout true //设置允许克隆子目录

echo 'tt*' >> .git/info/sparse-checkout //设置要克隆的仓库的子目录路径   //空格别漏

git remote add origin git@github.com:mygithub/test.git  //这里换成你要克隆的项目和库

git pull origin master    //下载
```