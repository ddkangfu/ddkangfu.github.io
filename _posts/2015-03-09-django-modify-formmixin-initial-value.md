---
layout: default
title: 修改Django的FormMixin或ModelFormMixin子类的Form的初始值
category: 开发
comments: true
---

# 修改Django的FormMixin或ModelFormMixin子类的Form的初始值

我们在使用继承自FormMixin的UpdateView、CreateView时，有时需要根据自己的需要对Form字段的的初始值进行修改，比如下面的一个Model：

```
BOOK_STATUS = (
        (0, _(u'正常')),
        (1, _(u'已借出')),
        (2, _(u'已预订'))
    )

class Book(models.Model):
    name = models.CharField(_(u'书名'), max_length=20)
    photo = models.ImageField(_(u'图片'), upload_to='mall/shop/photo/%Y/%m/%d/')
    status = models.SmallIntegerField(_(u'借阅状态'), default=0, choices=BOOK_STATUS)
```

对应的修改图书状态的Form:

```
class BookUpdateForm(ModelForm):
    class Meta:
        model = Book
        fields = ('status',)
```

假调我使用UpdateView来对Book的status字段更新，但在Template里需要显示成“已预订”，以方便用户直接提交不用再次从“正常”状态选择到“已预订”状态，默认Template只会显示status的默认值“正常”状态，所以需要在返回浏览器前就在后台设置好新的默认值，方法如下：

```
class BookUpdateView(UpdateView):
    template_name = 'book/book_update.html'
    form_class = BookUpdateForm
    model = Book
 
    def get_success_url(self):
        return reverse('book_list_view')
 
    def get_initial(self):
        # 或直接从此处返回 {'status': 2}
        init = super(BookUpdateView, self).get_initial()
        # 这里修改status字段的默认值
        init.update({'status': 2})
        return init
```

当然这只是一种方法，还可以在View的get_context_data里直接修改form里的初始值,这里写出来仅供参考。
