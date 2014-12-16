---
layout: default
title: MySQL性能优化
category: 开发
comments: true
---

# MySQL性能优化

这里收集了一些MySQL性能优化时用到查询语句和方法，今后还会不断的扩充。

1. 查看MySQL运行状态的命令：

```
show status like ‘uptime’
show status like ‘com_select/update/insert/delete’
show [session|global] status like ….

show status like ‘connections’ //查看连接数
show status like ‘slow_queries’ //查看慢查询数

show status like ‘long_query_time’ //查询慢查询时间，默认是10秒
set long_query_time=1 //设定慢查询时间
```

2. 其它外部查询方法 

```
bin/mysqld.exe —safe-mode —slow-quer-log
```

3. 显示表的索引/主键命令：

```
show index(es) from 表名
show keys from 表名
```