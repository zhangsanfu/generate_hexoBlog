---
title: Py006-01-06django之url控制path
date: 2018-10-22 22:42:28
tags: M06
---

> ### 先看问题

```
urlpatterns = [  
    re_path('articles/(?P<year>[0-9]{4})/', year_archive),  
    re_path('article/(?P<article_id>[a-zA-Z0-9]+)/detail/', detail_view),  
    re_path('articles/(?P<article_id>[a-zA-Z0-9]+)/edit/', edit_view),  
    re_path('articles/(?P<article_id>[a-zA-Z0-9]+)/delete/', delete_view),  
]
```

考虑下这样的两个问题：

第一个问题，函数 year_archive 中year参数是字符串类型的，因此需要先转化为整数类型的变量值，当然year=int(year) 不会有诸如如TypeError或者ValueError的异常。那么有没有一种方法，在url中，使得这一转化步骤可以由Django自动完成？

第二个问题，三个路由中article_id都是同样的正则表达式，但是你需要写三遍，当之后article_id规则改变后，需要同时修改三处代码，那么有没有一种方法，只需修改一处即可？

在Django2.0中，可以使用 path 解决以上的两个问题。

> 基本使用

```
复制代码
from django.urls import path  
from . import views  
urlpatterns = [  
    path('articles/2003/', views.special_case_2003),  
    path('articles/<int:year>/', views.year_archive),  
    path('articles/<int:year>/<int:month>/', views.month_archive),  
    path('articles/<int:year>/<int:month>/<slug>/', views.article_detail),  
]  
```

#### 基本规则：

- 使用尖括号(<>)从url中捕获值。
- 捕获值中可以包含一个转化器类型（converter type），比如使用 <int:name> 捕获一个整数变量。若果没有转化器，将匹配任何字符串，当然也包括了 / 字符。
- 无需添加前导斜杠。

![](https://images2018.cnblogs.com/blog/877318/201804/877318-20180424163727952-1649289117.png)

#### path转换器

Django默认支持以下5个转化器：

- str,匹配除了路径分隔符（/）之外的非空字符串，这是默认的形式
- int,匹配正整数，包含0。
- slug,匹配字母、数字以及横杠、下划线组成的字符串。
- uuid,匹配格式化的uuid，如 075194d3-6885-417e-a8a8-6c931e272f00。
- path,匹配任何非空字符串，包含了路径分隔符

#### 自定义转换器

对于一些复杂或者复用的需要，可以定义自己的转化器。转化器是一个类或接口，它的要求有三点：

- regex 类属性，字符串类型
- to_python(self, value) 方法，value是由类属性 regex 所匹配到的字符串，返回具体的Python变量值，以供Django传递到对应的视图函数中。
- to_url(self, value) 方法，和 to_python 相反，value是一个具体的Python变量值，返回其字符串，通常用于url反向引用。

例子

> step001随便建一个类

urlConvert.py

```
class MyConvert:  
    regex = '[0-9]{4}'  
    def to_python(self, value):  
        return int(value)  
    def to_url(self, value):  # 反向解析
        return '%04d' % value  
```

> step002使用register_converter 将其注册到URL配置中

```
# register_converter是必须引入的 帮你注册转换器
from django.urls import register_converter, path  
from . import urlConvert, views  
# 注册转换器
register_converter(urlConvert.MyConvert, 'yyyy')  
urlpatterns = [  
    path('articles/2003/', views.special_case_2003),  
    path('articles/<yyyy:year>/', views.year_archive),  
    ...  
] 

```