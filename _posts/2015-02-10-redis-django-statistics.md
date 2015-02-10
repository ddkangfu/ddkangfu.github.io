---
layout: default
title: Django中使用Redis统计在线用户数
category: 开发
comments: true
---

# Django中使用Redis统计在线用户数

由于Redis使用内存进行读取的No SQL数据库，在读取/写入健值对数据上速度非常可观，并且还有多种数据类型供实际环境使用，综合使用这些特性可以使我们的应用程在性能上得到极大的提升。

这回我们就使用Redis的ZSET数据类型来统计Django网站的10分钟内在线的用户数量。

准备环境：

1. Django 1.6+
2. Redis 2.4+
3. redis-py

### 开发环境

使用`mkvirtualenv`创建虚拟环境：

```
mkvirtualenv redis
pip install django==1.7.4
pip install redis==2.10.3
```

