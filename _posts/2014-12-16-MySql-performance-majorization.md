---
layout: default
title: MySQL性能优化
category: 开发
comments: true
---

# MySQL性能优化

这里收集了一些MySQL性能优化时用到查询语句和方法，今后还会不断的扩充。

1. 查看MySQL运行状态：

```
show status like ‘uptime’
show status like ‘com_select/update/insert/delete’
show [session|global] status like ….
show status like ‘connections’ //查看连接数
show status like ‘slow_queries’ //查看慢查询数
```

