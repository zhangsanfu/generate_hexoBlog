---
title: Py006-01-10django之自定义过滤器和标签
date: 2018-10-25 21:54:43
tags: M06
---

### 自定义标签和过滤器

1、在settings中的INSTALLED_APPS配置当前app，不然django无法找到自定义的simple_tag.

```
如你的应用是app001就在
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "app001" #注册
]
```

2、在app中创建templatetags模块(模块名只能是templatetags)

3、创建任意 .py 文件，如：my_tags.py

```
# 必须要引入这个模块
from django import template

#
register = template.Library()   #register的名字是固定的,不可改变
 
# 过滤器就加这个装饰器
@register.filter
def filter_multi(x,y):
    return  x * y

# 自定义标签就加这个装饰器
@register.simple_tag
def simple_tag_multi(x,y):
    return  x * y

@register.simple_tag
def my_input(id,arg):
    result = "<input type='text' id='%s' class='%s' />" %(id,arg,)
    return mark_safe(result)
```

4.使用自定义过滤器和标签必须load

index.html

```
{% load my_tag_filter %}
<p>{{ i|filter_multi:20}}</p>  ===> i=10  200 
# 过滤器最多接受一个参数
# 标签可以接受多个以空格分割
<p>{{simple_tag_multi:7 9}}</p>  ===>63


# 过滤器可以和if标签联合使用而 自定义标签和标签不能联合用
{% if i|filter_multi:10 > 100 %}
<p>100</p>
{% else %}
<p>{{ i }}</p>
{% endif %}

正常


{% if simple_tag_multi 9 10 > 100 %}
<p>100</p>
{% else %}
<p>{{ i }}</p>
{% endif %}

报错
```

