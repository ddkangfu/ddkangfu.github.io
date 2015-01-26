---
layout: default
title: BAE上部署Django
category: 开发
comments: true
---

# BAE上部署Django

申请了一个BAE的账号，新建了一个执行单元。目前的BAE 3.0支持python项目使用根目录下的requirements.txt文件来安装依赖包，即可以使用这个文件来安装Django，到目前为止BAE上可以安装的最新的Django版本为1.6.2，如果安装1.7.1会直接报部署失败的错误。

requirements.txt文件内容：

```
django==1.6.2
```

### 配置启动文件

修改完后，需要修改index.py文件，这是整个网站的入口，使用uwsgi来启动整个系统：

```
#-*- coding:utf-8 -*-

from ministore.wsgi import application as app

from bae.core.wsgi import WSGIApplication
application = WSGIApplication(app)
```

上传代码后，重新部署项目后，访问二级域名即可看到熟悉的`It worked!`的Django启动首页。

### 配置静态文件

后面会研究一下关于静态文件和模板文件的URL rewrite，有成果后会追记到这里。

静态文件处理上，需要修改app.conf文件：

```
handlers:
  - url : /static/(.*)
    script : /static/$1
  - url : /.*
    script : index.py

  - expire : .jpg modify 10 years
  - expire : .swf modify 10 years
  - expire : .png modify 10 years
  - expire : .gif modify 10 years
  - expire : .JPG modify 10 years
  - expire : .ico modify 10 years
```

其中`url : /static/(.*)`一定要配置在`url : /.*`之前，优先处理对静态文件的请求，不符合静态文件的请求url才会被分配给index.py处理。

另外`script : /static/$1`一定要和自己目录下的静态文件路径一致，否则也会找不到静态文件的，这需要在settings.py文件中进行配置：

```
......
SITE_ROOT = os.path.dirname(os.path.abspath(__file__))
SITE_ROOT = os.path.abspath(os.path.join(SITE_ROOT, '../'))

STATIC_ROOT = os.path.join(SITE_ROOT, 'static')
STATIC_URL = '/static/'
```

配置好后在app的static文件夹中放一个图片文件，然后运行命令收集文件：

```
python manage.py collectstatic
```

随后将程序用git/svn部署到BAE上（个人喜欢使用git部署，可以做各种尝试后再用--force参数撤消以前的提交记录，非常方便灵活），然后用链接访问一下图片测试是否能够访问成功。

### 数据库环境配置

为了方便调试和在BAE上运行，可以在配置文件中分别对两种数据库环境进行配置，区分这两个环境的方法是os.environ是否包含量SERVER_SOFTWARE：

```
if 'SERVER_SOFTWARE' in os.environ:
    from bae.core import const
    DATABASES = {
         'default': {
                    'ENGINE': 'django.db.backends.mysql',
                    'NAME': 'you_apply_database_name',
                    'USER': const.MYSQL_USER, 
                    'PASSWORD': const.MYSQL_PASS, 
                    'HOST': const.MYSQL_HOST, 
                    'PORT': const.MYSQL_PORT, 
         }
     }
else:
    DATABASES = {
        'default': {
                    'ENGINE': 'django.db.backends.mysql', 
                    'NAME': 'dev',
                    'USER': 'root',
                    'PASSWORD': 'passwd',
                    'HOST': 'localhost',
                    'PORT': '3306', 
        }
    }
```

### Redise设置

至2015年1月26日止，BAE还提供了10万次Redise键值对的免费存储，对于Django我们可以使用它来作为后端的缓存数据库。由于Django没有提供自带的Redis缓存引擎，我们需要另外安装支持Django的Package。

目前有两种Package可以选择，一个是[Django-redis](https://github.com/niwibe/django-redis),另一个是[Djang-redis-cache](https://github.com/sebleier/django-redis-cache)，我觉得django-redis-cache功能单一，且代码简单，还提供了连接缓冲池，基本满足我的需要，所以选择了它作为后端缓存的引擎，使用`pip install django-redis-cache`安装，目前的最高版本是0.13.0。

根据BAE提供的数据读取样例代码，我们需要在settings.py中作如下设置：

```
BAE_API_KEY = "xxxx"
BAE_SECRET_KEY = "xxxx"
BAE_REDIS_DB_NAME = "xxxxx"

CACHES = {
    'default': {
        'BACKEND': 'redis_cache.RedisCache',
        'LOCATION': 'redis.duapp.com:80',
        'OPTIONS': {
            'DB': 1,
            'PASSWORD': "%s-%s-%s"%(BAE_API_KEY, BAE_SECRET_KEY, BAE_REDIS_DB_NAME),
            'PARSER_CLASS': 'redis.connection.HiredisParser',
            'CONNECTION_POOL_CLASS': 'redis.BlockingConnectionPool',
            'CONNECTION_POOL_CLASS_KWARGS': {
                'max_connections': 50,
                'timeout': 20,
            }
        },
    },
}
```

以API_KEY、API_SECRET_KEY和DB_NAME的组合作为Redis数据库的密码，redis.duapp.com:80作为数据库地址，这样就可以访问后端的缓存Redis数据库了。
参考文章：[http://pyiner.com/2013/05/11/在BAE上用Django开发博客-在BAE上部署.html](http://pyiner.com/2013/05/11/在BAE上用Django开发博客-在BAE上部署.html)
