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

checkout有三种用法

1. `git checkout [-q] [<commit>] [--] <paths>...`

2. `git checkout [<branch>]`

3. `git checkout [-m] [[-b|--orphan] <new_branch>] [<start_point>]`

第1种用法用来从指定的commit里检出覆盖工作区中的文件，如果省略commit，则从暂存区中进行检出。
第2种用法用于切换分支，即使用HEAD指向指定的分支。
第3种用法用于创建并切换到新的分支。

举例：

1. `git checkout branch`

检出branch分支。

2. `git checkout`

汇总显示工作区、暂存区与HEAD的差异。

3. `git checkout -- filename`

用暂存区的filename来覆盖工作区中的filename文件 。相当于取消自上次执行`git add filename`以来对工作区文件的修改。

4. `git checkout branch -- filename`

HEAD指向不变，使用branch所指向的提交中的filename替换暂存和工作区中的filename文件。

5. `git checkout -- .` 或 `git checout .`

用暂存区的所有文件直接覆盖工作区的文件，相当于取消工作区所有修改，此命令相当危险且没有确认的机会，一定要慎用！！！

