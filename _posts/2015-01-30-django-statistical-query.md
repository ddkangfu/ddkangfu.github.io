---
layout: default
title: django官方文档——统计查询
category: 开发
comments: true
---

# django官方文档——统计查询


## 统计

Django 数据库抽象 API 描述了如何创建、检索、更新和删除独立的对象。但是，有时你会需要处理一些有关对象的集合的统计。本文描述如何使用 Django 查询来处理统计。

本文我们将使用以下模型。这些模型用于在线书店图书清单：

```
class Author(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    friends = models.ManyToManyField('self', blank=True)


class Publisher(models.Model):
    name = models.CharField(max_length=300)
    num_awards = models.IntegerField()


class Book(models.Model):
    isbn = models.CharField(max_length=9)
    name = models.CharField(max_length=300)
    pages = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    rating = models.FloatField()
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher)
    pubdate = models.DateField()


class Store(models.Model):
    name = models.CharField(max_length=300)
    books = models.ManyToManyField(Book)
```

## 生成整个查询集的统计

Django 提供两种方法来产生统计。第一种方法是产生整个 查询集 的统计。假设我们要统计所有书的平均价格。 Djnago 中查询所有书的语句为:

```
>>> Book.objects.all()
```

在这个语句后加上一个 aggregate() 子句就行了:

```
>>> from django.db.models import Avg
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}
```

上例中的 all() 是多余的。所以可以简写为:

```
>>> Book.objects.aggregate(Avg('price'))
{'price__avg': 34.35}
```

aggregate() 子句的参数代表我们要统计的内容，本例中我们要统计 Book 模型中 price 字段的平均值。 查询集参考 中可以找到完整的统计函数列表。

aggregate() 是一个 查询集 的未端子句，调用后会返回一个由名称－值配对组成的字典。名称是指统计的名称，值就是统计的值。名称由字段名称加上函数名自动组成。如果你想手动指定统计名称，可以象下例在统计子句中定义:

```
>>> Book.objects.aggregate(average_price=Avg('price'))
{'average_price': 34.35}
```

如果你想要生成多个统计，那么只要在统计子句后加上另外的统计子句就可以了。例如，如果要计算所有书价中最高价和最低价，可以这样写:

```
>>> from django.db.models import Avg, Max, Min, Count
>>> Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))
{'price__avg': 34.35, 'price__max': Decimal('81.20'), 'price__min': Decimal('12.99')}
```

## 生成查询集中每一个项目的统计

第二种方法是为 查询集 中每个独立的对象生成统计。例如，当你检索一个书单时，可能想知道每本书有几个作者。每本书与每个作者之间是一个多对多的关系，我们要为每本书总结这个关系。

要产生每个对象的统计可以使用 annotate() 子句。当定义一个 annotate() 子句后， 查询集 中的每个对象就可以与特定值关联，相当于每个对象有一个 “注释”。

这种注释的语法与 aggregate() 相同。 annotate() 的每个参数代表一个统计。例如，要计算每本书的作者人数:

```
# Build an annotated queryset
>>> q = Book.objects.annotate(Count('authors'))
# Interrogate the first object in the queryset
>>> q[0]
<Book: The Definitive Guide to Django>
>>> q[0].authors__count
2
# Interrogate the second object in the queryset
>>> q[1]
<Book: Practical Django Projects>
>>> q[1].authors__count
1
```

和 aggregate() 一样，统计的名称自动由字段名和函数名组成。你也可以在定义统计时指定名称:

```
>>> q = Book.objects.annotate(num_authors=Count('authors'))
>>> q[0].num_authors
2
>>> q[1].num_authors
1
```

与 aggregate() 不同的是 annotate() 不是 一个未端子句。 annotate() 子句的输出是一个 查询集 。 这个 查询集 可以和其他查询集一样操作，包括 filter() 、 order_by 或者甚至再调用另一个 annotate() 。

## 联合和统计

至此，我们统计的对象都是被查询的模块本身的字段。但是，有时我们要统计的是被查询模块的相关联的模块字段。

在统计函数中定义字段时，可以使用与过滤器中用于指定关联字段的 双下划线符号 。通过这种方法， Django 会自动使用联合来统计相关联的字段。
例如，要统计每个书店中书的价格范围:

```
>>> Store.objects.annotate(min_price=Min('books__price'), max_price=Max('books__price'))
```

上面的例子告诉 Django 检索 Store 模型，联合（通过多对多关系） Book 模型，并且统计 book 模型中的价格字段，计算最大值和最小值。
aggergate() 子句适用同样规则。如果你想知道所有书店中书的最高价和最低价，可以这样:

```
>>> Store.objects.aggregate(min_price=Min('books__price'), max_price=Max('books__price'))
```

联合的深度是无限的。例如，要统计所有书的作者的最小年龄，你可以这样:

```
>>> Store.objects.aggregate(youngest_age=Min('books__authors__age'))
```

## 统计和其他查询子句

### filter() 和 exclude()

在过滤器中也可以使用统计。任何用于一般模型的 filter() （或 exclude() ）也可与统计联用。

当与 annotate() 子句联用时，过滤器作用于被统计的对象上。例如要统计书名以 "Django" 开头的书:

```
>>> Book.objects.filter(name__startswith="Django").annotate(num_authors=Count('authors'))
```

当与 aggregate() 子句联用时，过滤器作用于被统计的所有对象上。例如要统计书名以 "Django" 开头的书的平均价格:

```
>>> Book.objects.filter(name__startswith="Django").aggregate(Avg('price'))
```

### 过滤统计的值

统计出来的值也可以被过滤。和其他模型一样，统计的结果也可以使用 filter() 和 exclude() 子句来过滤。

例如，要产生一个由两个以上作者的书单可以这样:

```
>>> Book.objects.annotate(num_authors=Count('authors')).filter(num_authors__gt=1)
```

上例先进行统计，然后在统计的结果上使用了过滤器。

### annotate() 和 filter() 子句的顺序

当使用同时包含 annotate() 和 filter() 子句的复杂查询时，要特别小心两种子句的顺序。

当一个 annotate() 子句作用于查询时，该统计只对子句之前的查询起作用。也就是说 filter() 和 annotate() 顺序不同，查询就不同了。查询:

```
>>> Publisher.objects.annotate(num_books=Count('book')).filter(book__rating__gt=3.0)
```

和查询:

```
>>> Publisher.objects.filter(book__rating__gt=3.0).annotate(num_books=Count('book'))
```

是不同的。两个查询都会返回至少有一本好书（评分大于 3.0 ）的出版商。但是，第一个查询中的统计会提供出版商的所有书的数量；第二个查询中的统计只返回好书的数量。第一个查询中统计先于过滤器，所以过滤器对统计没有作用。而第二个查询过滤器先于统计，所以统计的对象是已经过滤过的。

### order_by()

统计可以作为排序的基础。当你定义一个 order_by 子句时，可以引用 annotate() 子句中的统计。

例如，要依据书的作者人数进行排序，可以这样:

>>> Book.objects.annotate(num_authors=Count('authors')).order_by('num_authors')

### values()

通常,统计会针对 查询集 中每一个对象返回一个结果。但是，当使用 values 子句来约束要统计的列时，返回的结果会有所不同。原先统计结果中，统计字段的值相同的项会分组合并统计。

例如，要统计每个作者各自所写的书的平均评分:

```
>>> Author.objects.annotate(average_rating=Avg('book__rating'))
```

返回的结果会包含每一个作者及其所写的书的平均计分。

但是，如果使用 values() 子句，返回的结果会有所不同:

```
>>> Author.objects.values('name').annotate(average_rating=Avg('book__rating'))
```

这个例子中会把作者按名字分组统计，返回的结果中不会有重复的作者名字。名字相同的作者在统计中会作为同一个作者来统计，同名作者所写的书的评分会合并为一个作者的书来统计。

### annotate() 和 values() 子句的顺序

当使用 filter() 子句时， annotate() 和 values() 子句的顺序是非常重要的。如果 values() 子句先于 annotate() 子句，会按照前文所述的方式统计。

但是，如果 annotate() 子句先于 values() 子句，那么统计会作用于整个查询集，而 values() 子句只约束统计输出的字段。

例如，如果我们把前一个例子中的 values() 和 annotate() 子句调换顺序:

```
>>> Author.objects.annotate(average_rating=Avg('book__rating')).values('name', 'average_rating')
```

这个例子会为每一个作者生成唯一的结果。但是在输了的数据中只会包含作者名和 average_rating 的统计。

你可以注意到 average_rating 在例子中显示地定义了。在 annotate() 和 values() 子句的顺序处于这种情况是必须显式定义。

如果 values() 子句先于 annotate() 子句，那么任何统计会自动添加到输出结果中。但是 values() 子句在 annotate() 子句之后，那么必须显式定义统计列。

### 缺省排序或 order_by() 子句的副作用

一个查询集中 order_by() 子句中的字段（或一个模型中缺省排序字段）会对输了数据产生影响，即使在 values() 中没有这些字段的定义时也同样会影响。这些特殊的字段会影响统计结果,这种情况在计数统计时尤为明显。

假设有一个这样的模型:


```
class Item(models.Model):
    name = models.CharField(max_length=10)
    data = models.IntegerField()

    class Meta:
        ordering = ["name"]
```

这里的重点是作为缺省排序的 name 字段。如果你想要统计不重复的 data 值出现了多少次，你可能会使用如下语句:

# 警告：这个语句不完全正确！

```
Item.objects.values("data").annotate(Count("id"))
```

...这个语句看似会根据 data 值分组统计 Item 对象的 id 。但统计结果中 name 字段也会参与其中，所以这个语句实际的是不重复的 (data, name) 配对，而这不是我们所要的结果，因此我们应当这样统计:

```
Item.objects.values("data").annotate(Count("id")).order_by()
```

...这里我们通过一个空的“ order_by() ” 来清除副作用。

这个行为与在查询集文档中 distinct() 提到的一样。通常的规则是：当你不想要额外的字段在统计结果中产生作用时，必须清空排序的内容或者至少确认 values() 子句中的字段已经限制了这些额外字段。

### Note
你可以会问为什么 Django 不去除这些字段的影响。主要的原因是为维护 distinct() 的一贯性和一个原则： Django 从不 删除你的排序定义（我们不会改变那么模型方法的行为，否则就会违背我们 API stability 策略）。

## 对小计进行统计

你可以对小计的结果进行统计。在查询中，你可以使用 aggregate() 子句来对 annotate() 的结果进行统计。

例如，假设你要统计每本书的作者人数的平均值，那么首先要计算每本书的作者人数，然后根据这个结果来统计平均值:

```
>>> Book.objects.annotate(num_authors=Count('authors')).aggregate(Avg('num_authors'))
{'num_authors__avg': 1.66}
```