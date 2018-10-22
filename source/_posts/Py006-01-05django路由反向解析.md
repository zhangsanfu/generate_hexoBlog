---
title: Py006-01-05django路由反向解析
date: 2018-10-21 15:57:50
tags: M06
---

> #### 登陆需求前置工作

```
# step001 新建django项目  mydj003
# step002 设置应用为  app001
    # mydj003/urls.py

    from django.contrib import admin
    from django.urls import path
    from app001 import views
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('login/',views.login)
    ]

    # app001/view.py
    def login(request):
        return render(request,'login.html')
# step003 开发登陆需求  localhost:8000/login
# step004 login.html能提交表单 action先写死为  localhost:8000/login

    templates/login.html
    
    <form action="http://127.0.0.1:8000/login/" method="post">
        用户名 <input type="text" name="user">
        密码 <input type="password" name="pwd">
        <input type="submit">
    </form>

# step005 python3 manage.py runserver  提交表单发现报 403 
    此时你要修改一下settings.py里的  csrf配置
    注释掉 csrf哪一行(这个问题以后解决)
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        # 'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]

# step006 此时你就可以正常提交表单了 
    访问 localhost:8000/login 会返回登陆页面 get请求
    提交按钮 也会访问 localhost:8000/login  post请求
```

#### 分支处理get和post请求

app001/views.py

```
from django.shortcuts import render,HttpResponse

def login(request):
    print(request.method)

    if request.method =='GET':
        return render(request,'login.html')
    else:
        print(request.GET)
        print(request.POST)
        user=request.POST.get('user');
        pwd=request.POST.get('pwd');

        if user=='aaa' and pwd =='123':
            return HttpResponse("login success")
        else:
            return HttpResponse("login error")

# 在进行访问  localhost:8000/login 然后提交表单——一切正常
```

#### 反向解析

> 刚刚的action里是写死的，但是在开发过程中业务是会变的，这样就导致一旦变了改的就很多了

```
此时你修改路由
# path('login/',views.login) 修改 为 login.html
path('login.html/',views.login)

# 再去提交 表单 发现提交不好使了
此时你必须把login.html里的 form的action修改才能正常访问！！！！
```

> 解决问题

- 路由别名 name

```
# 修改urls.py里的路由
urlpatterns = [
    path('admin/', admin.site.urls),
    path('login/',views.login,name="Login")
]

# 修改login.html里action
<form action="{% url "Login" %}" method="post">


# 注意模版里的 {% url "Login" %} 是在视图函数  return render的时候 解析Login的url
```


#### 反向解析函数

> step001添加路由

app001/urls.py添加如下路由

```
from django.urls import path,re_path
from . import views

urlpatterns = [
    re_path(r'^articles/2003/$',views.x001,name="year2003"),
    re_path(r'^articles/([0-9]{4})/$',views.x002,name="y_m")
]
```

> step002路由分发

```
from django.contrib import admin
from django.urls import path,re_path,include
from app001 import views
urlpatterns = [
    path('admin/', admin.site.urls),
    path('login/',views.login,name="Login"),

    # 分发
    re_path(r"^app001/",include('app001.urls'))

]

```

> step003添加视图函数

- reverse反向解析函数  传递定义的路由别名来获取url
- 注意点在视图函数里可以反向解析 urls.py里的任意定义别名的url路由
- 如果urls.py里的路由含有动态参数，反向解析的时候必须传递对应的参数才可以

```
from django.urls import reverse

def x001(request):
    url = reverse('year2003')
    print(url)
    # url2 = reverse('y_m')
    #  这条url里有分组，r'^articles/([0-9]{4})/$'
    #  所以反向解析的时候，要匹配一个参数才可以
    #  正确的使用方式 传递args参数 这里是一个元祖
    url2 = reverse('y_m',args=(4009,))
    return HttpResponse('2003')

def x002(request,year):
    return HttpResponse('year%s'%year)

```


#### 路由命名空间

> step000再开一个应用

```
python manage.py startapp app002
```

> step001添加路由

- app001/urls.py添加如下路由并使用别名index
- app002/urls.py添加如下路由并使用别名index
- 此时app002和app001的路由别名是重复的

```
urlpatterns = [
    re_path('index/',view.index,name="index"),
]

```

> step002路由分发

```
from django.contrib import admin
from django.urls import path,re_path,include

urlpatterns = [
    path('login/',views.login,name="Login"),

    # 分发
    re_path(r"^app001/",include('app001.urls'))
    re_path(r"^app002/",include('app002.urls'))

]

```

> step003反向解析路由

分别在app001和app002的urls.py的视图函数里反向解析url

```
from django.urls import reverse

def index(request):
    url = reverse('index');
    return HttpResponse(url)
```

> step004访问如下url

```
localhost:8000/app001/index/
响应 /app002/index


localhost:8000/app002/index/
响应 /app002/index
```

为什么找到的都是一个路由

- 因为app001和app002的路由index定义的别名相同，分发的时候就会发生覆盖

> #### 名称空间来解决不同应用别名相同覆盖的问题

1.主urls.py里

```
# 使用命名空间
urlpatterns = [
    # 分发 子应用 路由别名相同时，反向解析路由的时候就会发生覆盖
    # re_path(r"^app001/",include('app001.urls'))
    # re_path(r"^app002/",include('app002.urls'))

    # 分发  使用命名空间来规避  反向解析路由覆盖问题
    re_path(r"^app001/",include(('app001.urls',"app001")))
    re_path(r"^app002/",include(('app002.urls',"app002")))
]
```

2.app001/views.py和app002/views.py里

```
# app001
def index(request):
    url = reverse("app001:index")
    return HttpResponse(url)


# app002
def index(request):
    url = reverse("app002:index")
    return HttpResponse(url)
```

> 此时反向解析就能解析对应的url里了
