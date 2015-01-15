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


修改完后，需要修改index.py文件，这是整个网站的入口，使用uwsgi来启动整个系统：

```
#-*- coding:utf-8 -*-

from ministore.wsgi import application as app

from bae.core.wsgi import WSGIApplication
application = WSGIApplication(app)
```

上传代码后，重新部署项目后，访问二级域名即可看到熟悉的`It worked!`的Django启动首页。

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

另外`script : /static/$1`一定要和自己目录下的静态文件路径一致，否则也会找不到静态文件的。

参考文章：[http://pyiner.com/2013/05/11/在BAE上用Django开发博客-在BAE上部署.html](http://pyiner.com/2013/05/11/在BAE上用Django开发博客-在BAE上部署.html)
