---
layout: default
title: Git Checkout
category: 开发
comments: true
---

# Git Checkout

2015年的第一篇Blog，希望新的一年有好的运气。

`git checkout`命令，当被操作的对象不是一个分支而是一个commit的时候就会处于“分离头指针”状态。具体的定义就是HEAD头指针指向了一个具体的ID而不是一个引用（分支）。

当使用checkout命令切换到其它分支时，“分离头指针”状态下修改的内容都会丢失。但可以使用-b参数为所做的修改新建一个分支。

 