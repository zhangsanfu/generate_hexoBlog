---
title: Py006-01-04django路由简单配置
date: 2018-10-21 00:34:04
tags: M06
---

> 此逻辑建立在上一篇博客的代码基础上，请自行拷贝

### URLconf

URL配置(URLconf)就像Django 所支撑网站的目录。它的本质是URL与要为该URL调用的视图函数之间的映射表；你就是以这种方式告诉Django，对于客户端发来的某个URL调用哪一段逻辑代码对应执行。

> re_path是django1里的一个函数

-  urls.py里  默认引入的是 from django.urls import path
-  django2向前兼容也可以引入re_path

```
# step001 修改  urls.py
from django.contrib import admin
from django.urls import path,re_path

from app001 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', views.timer),

    # 路由配置    路径------》视图函数
    re_path(r'^articles/2003/$', views.special_case_2003),
    # re_path(r'^articles/([0-9]{4})/$', views.year_archive),
    # re_path(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
    # re_path(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
]

# step002 在 app001/views.py里定义 special_case_2003函数

# 引入HttpResponse函数
from django.shortcuts import render,HttpResponse
def special_case_2003(request):
    return HttpResponse("special_case_2003")


# step003 打开链接  http://localhost:8000/articles/2003/
你会看到 special_case_2003
```

> 很明显 r'^articles/2003/$ 是一个正则匹配

```
如果你去掉 r'^articles/2003/$" 里的"$"  
-就可以匹配 articles/2003/01/11

如果你去掉 r'^articles/2003/$" 里的"^"  
-就可以匹配 aaa/articles/2003/

如果你去掉 r'^articles/2003/$" 里的"^"和"$"  
-就可以匹配 aaa/articles/2003/01/22
```

> 注意：

- 若要从URL 中捕获一个值，只需要在它周围放置一对圆括号。
- 不需要添加一个前导的反斜杠，因为每个URL 都有。例如，应该是^articles 而不是 ^/articles。
- 每个正则表达式前面的'r' 是可选的但是建议加上。它告诉Python 这个字符串是“原始的” —— 字符串中任何字符都不应该转义

> 新需求：刚刚的articles/2003 不一定永远是这样的

```
你会有
articles/2003
articles/2004
articles/2005
articles/2006
articles/2007
...
articles/2018
这样每个都配置就很恶心了 
```

#### 正则动态匹配路由

```
# 路由修改如下
urlpatterns = [
    ...

    # 路由配置    路径------》视图函数
    re_path(r'^articles/2003/$', views.special_case_2003), # 实际是调用 special_case_2003(request)
    re_path(r'^articles/([0-9]{4})/$', views.year_archive), # 实际是调用 year_archive(request,1999)
]

'''
re_path(r'^articles/([0-9]{4})/$', views.year_archive) 中  

([0-9]{4}) 代表——分组(匹配四位数字) 此时就会多传递一个参数
只要有()就代表分组 就会多传递一个参数 形如 year_archive(request,1999)

[0-9]{4} 如果没有"()"代表不分组，这样调用视图函数的时候这个动态的值就不会当作位置参数传递
形如 year_archive(request)
'''

# views.py里 更新对应的视图函数
def year_archive(request,year):
    return HttpResponse("year is %s"%year)

# 访问 http://localhost:8000/articles/2021/
得到内容  year is 2021
```

> 注意访问 http://localhost:8000/articles/2003/ 

```
返回的是 special_case_2003
因为路由里会按规则顺序进行匹配 刚好第一条规则在我们动态匹配年份的前面

re_path(r'^articles/2003/$', views.special_case_2003), 
re_path(r'^articles/([0-9]{4})/$', views.year_archive), 
```

#### 匹配两个动态路由   不仅接受年还支持月份

```
# 在修改 urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', views.timer),
    re_path(r'^articles/2003/$', views.special_case_2003),
    re_path(r'^articles/([0-9]{4})/$', views.year_archive),
    re_path(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive), # # 实际是调用 month_archive(request,1999,12)
]

# 在views.py 进行视图函数的定义

def month_archive(request,year,month):
    return HttpResponse("year is %s ,month is %s"%(year,month))

# 此时访问 http://localhost:8000/articles/2003/02/ 
返回 year is 2003 ,month is 02


# 注意
# 注意
# 注意
# 此时如果你去掉 re_path(r'^articles/([0-9]{4})/$', views.year_archive), 的 "$" 就会导致月份没法匹配了
```


#### 有名分组

参考刚才最后一个例子

```
# 在修改 urls.py
urlpatterns = [
    ...
    re_path(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive), # # 实际是调用 month_archive(request,1999,12)
]

# 在views.py 进行视图函数的定义

def month_archive(request,year,month):
    return HttpResponse("year is %s ,month is %s"%(year,month))

```

> 如果此时你修改month_archive(request,year,month)中的参数 year和month的位置那么 返回的结果就颠倒了

- 关键字传参

```
修改路由如下

urlpatterns = [
    ...
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive)
    #  实际是调用 month_archive(request,year=1999,month=12)
]
```

再也不担心参数位置变更导致的问题了


#### 路由分发

现在就一个应用，假如以后多了呢？ 这样路由都写在 mydb002/urls.py里就很恶心 了

-  1.在app001目录里建立  urls.py

```
# 每个应用的url独立
# 
from django.contrib import admin
from django.urls import path,re_path

from app001 import views

urlpatterns = [
    re_path(r'^articles/2003/$', views.special_case_2003),
    re_path(r'^articles/([0-9]{4})/$', views.year_archive),
    re_path(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive)
    #  实际是调用 month_archive(request,year=1999,month=12)
]

```

- 2. 修改 主(mydj002目录的) urls.py

```
from django.contrib import admin
# 引入 include函数
from django.urls import path,re_path,include

from app001 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', views.timer),
    # 内容分发
    re_path(r"app001/",include("app001.urls"))
]


# 此时你访问就都可以了
http://localhost:8000/app001/articles/2003/
http://localhost:8000/app001/articles/2004/12/
```

> 但是带着app001不太好看

修改主 urls.py

```
urlpatterns = [
    ...
    # 内容分发
    # re_path(r"app001/",include("app001.urls"))
    re_path(r"^",include("app001.urls"))
]

# 访问就正常了
# http://localhost:8000/articles/2003/
# http://localhost:8000/articles/2003/12/
```