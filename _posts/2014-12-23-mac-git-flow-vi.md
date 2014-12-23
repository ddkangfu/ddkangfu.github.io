---
layout: default
title: Mac中git使用vi编辑注释导致提交失败的解决方法
category: 开发
comments: true
---

# Mac中git使用vi编辑注释导致提交失败的解决方法

最近在使用git flow进行一些分支的自动提交合并操作，发现在finish某个分支后，在弹出的vi中编写完注释保存时出现错误信息，导致提交合并的失败，通常会看到下面的错误信息：

```
error: There was a problem with the editor 'vi'.
Please supply the message using either -m or -F option.
Tagging failed. Please run finish again to retry.
```

在网上找了下，出现这种情况的同学还真不少，但是解决方案很简单，在这里记录一下备忘吧。

只需要明确设置一下vim所在的路径即可，命令如下（这里将vi替换成了更高级的vim，原理相同）：

```
git config --global core.editor /usr/bin/vim
```

改完之后再次提交合并时，一切正常。
