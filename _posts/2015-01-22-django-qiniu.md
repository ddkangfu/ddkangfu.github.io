---
layout: default
title: Django中使用七牛存储
category: 开发
comments: true
---

# Django中使用七牛存储

在Django项目中使用七牛存储的api，可以存储static和用户上传的文件，并可以在七牛管理中设置模板，直接生成需要尺寸的资源图片，对我们来说可谓极其方面，免费用户最多可提供10G存储和每月10G的流量，真是便宜又大碗，对我们小用户来说基本上够用了。

Django中推荐使用七牛的Django插件，比官方的api要简单得多的多，只需要在settings中设置一下即可。

安装七牛插件：

```
pip install django-qiniu-storage
```

在项目的settings.py文件中需要设置必要的信息：

```
QINIU_ACCESS_KEY='xxxx' # 七牛给开发者分配的 AccessKey
QINIU_SECRET_KEY='xxxx' # 七牛给开发者分配的 Secret
QINIU_BUCKET_NAME='xxxx' # 存放文件的七牛空间(bucket)的名字
QINIU_BUCKET_DOMAIN='xxx.clouddn.com' # 七牛空间(bucket)的域名

# 一般设置成这样就够用了，其它特殊设置请参见官方说明文档
DEFAULT_FILE_STORAGE = 'qiniustorage.backends.QiniuMediaStorage'
STATICFILES_STORAGE  = 'qiniustorage.backends.QiniuStaticStorage'

MEDIA_ROOT = os.path.join(SITE_ROOT, 'media')
MEDIA_URL = '/media/' #这个需要进一步确定用法

STATIC_ROOT = os.path.join(SITE_ROOT, 'static')
STATIC_URL = 'http://七牛空间域名/static/' # 这里这么设置是为了使用七牛感存储的static文件
```

设置完成后，执行命令即可上传文件到七牛Bucket中：

```
python manage.py python manage.py collectstatic
```

在七牛存储的管理页面的数据处理中设置一个样式，可以使用该样式处理资源图片，如我设置了一个名为3300x240的样式，设置如下：

```
以"-"为分隔符
处理方式： 指定宽高缩放,居中剪裁
图片尺寸： 宽度300px , 高度240px
图片质量： 85
图片格式： 默认
```

那我在template中可以采用如下方式使用该样式生成的缩略图：

```
{{ STATIC_URL }}images/image0.jpg-300x240
```

关于图片上传方面的处理我会在后面再进行介绍。

文档地址[http://django-qiniu-storage.readthedocs.org/zh_CN/latest/](http://django-qiniu-storage.readthedocs.org/zh_CN/latest/)
