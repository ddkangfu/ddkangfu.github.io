---
layout: default
title: django-celery 与 rabbitmq 配置简要说明
category: 开发
comments: true
---

# django-celery 与 rabbitmq 配置简要说明 

###1. 安装rabbitmq

```
brew update
brew install rabbitmq
```

配置路径：

修改~/.bash_profile或~/.zshrc文件，添加以下内容：

```
# RabbitMQ Config
export PATH=$PATH:/usr/local/sbin
```

修改后使用命令 source ~/.bash_profile或~/.zshrc使路径生效

### 2. 安装django-celery

使用pip命令安装即可

```
pip install django-celery
```

### 3. 项目中配置django-celery

打开Django项目的settings.py文件，在INSTALLED_APPS中加入以下内容：

```python
INSTALLED_APPS = (
     ...
          'djcelery',
               ...
 )
```

在settings.py末尾添加RabbitMQ的配置：

```python
import djcelery
djcelery.setup_loader()

BROKER_HOST = "localhost"
BROKER_PORT = 5672
BROKER_USER = "guest"
BROKER_PASSWORD = "guest"
BROKER_VHOST = "/"
```

注：配置Redis的方法。

```
BROKER_HOST = "localhost"
BROKER_BACKEND="redis"
REDIS_PORT=6379
REDIS_HOST = "localhost"
BROKER_USER = ""
BROKER_PASSWORD =""
BROKER_VHOST = "0"
REDIS_DB = 0
REDIS_CONNECT_RETRY = True
CELERY_SEND_EVENTS=True
CELERY_RESULT_BACKEND='redis'
CELERY_TASK_RESULT_EXPIRES =  10
CELERYBEAT_SCHEDULER="djcelery.schedulers.DatabaseScheduler"
```

需要安装redis包：

```
$ pip install redis
```

确认配置文件的数据库连接无误后，执行以下命令：

```python
python manage.py syncdb
```

### 4. 编码

在项目的app里增加tasks.py文件：

```python
from celery.decorators import task

@task
def add(x, y):
     return x+y
```

### 5. 运行

启动rabbitmq

```
sudo rabbitmq-server
```

启动woker

```
python manage.py celeryd -l info
```

启动后另启一个终端，运行以下命令:

```
python manage.py shell

from hello.tasks import add
r = add.delay(3,5) # 执行这一行就能在worker的日志中看到运行状况
r.wait()
```

返回结果为8时，表明已经可以正常运行。


