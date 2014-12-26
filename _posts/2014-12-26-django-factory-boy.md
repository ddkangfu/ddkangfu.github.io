---
layout: default
title: Django中使用Factory_boy进行单元测试
category: 开发
comments: true
---

# Django中使用Factory_boy进行单元测试

看《Python开发实战》中有一节是介绍Django中使用Factory_boy来进行单元测试数据生成的。由于之前一个项目深受手工生成测试数据的苦，所以就想尝试一下这个Boy。

1. 安装Factory_boy
```
pip install factory-boy
```

2. 写测试代码

为了方便，使用的是Django教程中的那个例子。

先建立factories.py文件，用来定义用到的Factory:

```
from factory.django import DjangoModelFactory

from django.utils import timezone

from .models import Question


class QuestionFactory(DjangoModelFactory):
    class Meta:
        model = Question
        """
        Fields whose name are passed in this list will be used to perform a
        Model.objects.get_or_create() instead of the usual Model.objects.create()
        """
        #django_get_or_create = ('question_text',)

    question_text = "How do you do ?"
    pub_date = timezone.now()
```

然后写测试代码tests.py，这里只简单举一个例子：

```
import datetime

from django.test import TestCase
from django.utils import timezone

from .factories import QuestionFactory


class QuestionMethodTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = QuestionFactory(pub_date=time)
        self.assertEqual(future_question.was_published_recently(), False)
```

其余部分代码参见Django文档的教程。

3. 运行测试

```
python manange.py test polls
```

测试通过，一切OK。

4. 遇到的问题

由于《Python开发实战》这本书日本出版的时候应该比较早了，所以书中讲解factory_boy（说是讲解，其实就是照抄了一下Factory_boy的官方介绍文档）的版本也比较老，与最新的版本有不小出入，我在使用的时候就碰到了麻烦，查阅了官方文档才得以解决。

最初的factories.py文件是这样定义的：

```
from factory.django import Factory

from django.utils import timezone

from .models import Question


class QuestionFactory(Factory):
    class Meta:
        model = Question

    question_text = "How do you do ?"
    pub_date = timezone.now()

```

然后运行之后，怎么也不能将生成的对象保存到数据库中，后来查阅官方文档才知道，Django使用的Factory的基类，已由原来的factory.Factory变成了factory.django.DjangoModelFactory。应该是由于使用了错误的基类，在Factory中调用Model.objects.create方法时，找不到该方法才导致无法将数据保存到数据库的。

以后还是主要看官方文档吧，书也是靠不住的 : (
