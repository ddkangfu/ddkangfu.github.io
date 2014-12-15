---
layout: default
title: Django Celery的使用
category: 开发
comments: true
---

# Django Celery的使用

1. 安装celery和django-celery

```
pip install celery

pip install django-celery
```

2. 设置settings.py

在settings.py文件的最后增加：

```
BROKER_URL = 'amqp://guest:guest@localhost//'

#选择性设置
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
```

修改INSTALLED_APPS设置djcelery：

```
INSTALLED_APPS = (
    'django.contrib.auth',
    'django.contrib.contenttypes',
    ...
    'djcelery',
    ...
)
```