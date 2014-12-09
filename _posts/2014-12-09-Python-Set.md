---
layout: default
title: Python集合（set）类型的操作
category: 开发
comments: true
---

# Python集合（set）类型的操作

python的set和其他语言类似, 是一个无序不重复元素集, 基本功能包括关系测试和消除重复元素. 集合对象还支持union(联合), intersection(交), difference(差)和sysmmetric difference(对称差集)等数学运算.

sets 支持 x in set, x not in set, len(set),和 for x in set。作为一个无序的集合，sets不记录元素位置或者插入点。因此，sets不支持 indexing, slicing, 或其它类序列（sequence-like）的操作。

下面来点简单的小例子说明:

```
>>> x = set('spam')
>>> y = set(['h','a','m'])
>>> x, y
(set(['a', 'p', 's', 'm']), set(['a', 'h', 'm']))
```

再来些小应用:

```
>>> x & y # 交集
set(['a', 'm'])

>>> x | y # 并集
set(['a', 'p', 's', 'h', 'm'])

>>> x - y # 差集
set(['p', 's'])

>>> x.add('x') # 添加一项
>>> y.update([10,37,42]) # 在y中添加多项
>>> y.remove('h') #删除一项
>>> len(x) #set 的长度
>>> s in x #测试 s 是否是 x 的成员
>>> s not in x #测试 x 是否不是 s 的成员
```

记得以前个网友提问怎么去除海量列表里重复元素，用hash来解决也行，只不过感觉在性能上不是很高，用set解决还是很不错的，示例如下:

```
>>> a = [11,22,33,44,11,22]
>>> b = set(a)
>>> b
set([33, 11, 44, 22])
>>> c = [i for i in b]
>>> c
[33, 11, 44, 22]
```

很酷把，几行就可以搞定。


