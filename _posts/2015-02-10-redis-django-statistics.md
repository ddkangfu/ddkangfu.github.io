---
layout: default
title: Django中使用Redis统计在线用户数
category: 开发
comments: true
---

# Django中使用Redis统计在线用户数

由于Redis使用内存进行读取的No SQL数据库，在读取/写入健值对数据上速度非常可观，并且还有多种数据类型供实际环境使用，综合使用这些特性可以使我们的应用程在性能上得到极大的提升。

这回我们就使用Redis的ZSET数据类型来统计Django网站的10分钟内在线的用户数量。

### 安装Redis

本人使用的是Mac系统，直接使用`brew install redis`即可安装，其它系统请参考Redis的安装说明[http://redis.io/download](http://redis.io/download)。

### 开发环境

使用`mkvirtualenv`创建虚拟环境：

```
mkvirtualenv redis
pip install django==1.7.4
pip install redis==2.10.3
```

### 创建Django工程

使用 `django-admin.py` 创建Django的Project,并创建一个名为main的App。

```
django-admin startproject redistest
cd redistest
python manage.py startapp main
```

### 增加登录/注销功能

为了统计登录的在线用户，我们需要用户进行登录，最方便的就是使用admin系统来进行登录和注销。由于我们使用的是Django1.7.4，默认已经启用了admin，所以我们只要使用命令同步数据库即可。

```
python manage.py syncdb
```

然后启动开发服务器`python manage.py runserver`，登录一下admin后台[http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)即表示当前用户在线。

#### 编写中间件

为了在每次请求的时候都对请求数进行拦截，我们需要自己写一个middleware来对request进行处理，关于middleware的一些知识请参见Django的文档(https://docs.djangoproject.com/en/1.7/topics/http/middleware/)[https://docs.djangoproject.com/en/1.7/topics/http/middleware/]，代码参见下面：

```
import time

class StatisticsMiddleware(object):
    def process_request(self, request):
        if request.user.is_authenticated():
            redis_con.zadd('users.online', time.time(), request.user.username)
```

代码很简单，就是在拦截request请求，当用户登录的时候，将用户的用户名和当前时间写入到一个名为users.online的ZSET有序集合里，以时间为值进行从小到大的排序。当这个数据就绪后，我们就可以从视图里对某一时间段访问过的用户进行查询了。
