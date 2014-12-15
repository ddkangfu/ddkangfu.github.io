---
layout: default
title: Django Celery在Django1.3.7中的使用
category: 开发
comments: true
---

# Django Celery在Django1.3.7中的使用

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

3. 增加_celery.py文件：

```
from __future__ import absolute_import

import os

from celery import Celery

from django.conf import settings

# set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'settings')

app = Celery('celerydemo')

# Using a string here means the worker will not have to
# pickle the object when using Windows.
app.config_from_object('django.conf:settings')
app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)
app.conf.update(CELERY_RESULT_BACKEND='djcelery.backends.database:DatabaseBackend',)


@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```

4. 修改项目目录下的__init__.py文件：

```
from __future__ import absolute_import


# This will make sure the app is always imported when
# Django starts so that shared_task will use this app.
from ._celery import app as celery_app
```

5. 增加App中的tasks.py文件：

```
from __future__ import absolute_import

from celery import shared_task


@shared_task
def add(x, y):
    return x + y


@shared_task
def mul(x, y):
    return x * y


@shared_task
def xsum(numbers):
    return sum(numbers)
```
