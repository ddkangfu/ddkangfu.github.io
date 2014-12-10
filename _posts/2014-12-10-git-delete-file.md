---
layout: default
title: Python集合（set）类型的操作
category: 开发
comments: true
---

# [转]彻底删除仓库中的无效文件

```
git filter-branch --index-filter 'git rm -r --cached --ignore-unmatch path/to/your/file' HEAD
git push origin master --force
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
```

原文地址：<http://yihui.name/cn/2010/12/animation-update-1-1-5/， https://help.github.com/articles/remove-sensitive-data>