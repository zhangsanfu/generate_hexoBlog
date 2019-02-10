---
title: Py009-01-04接口开发之restful由来
date: 2019-02-10 13:47:35
tags: M09
---

### 以前的用户管理接口实现

```
http://www.xxx.com/add_user
http://www.xxx.com/update_user
http://www.xxx.com/get_user
http://www.xxx.com/delete_user


在 django 里

urlpatterns = [
    re_path(r'^get_user/',views.get_user),
    re_path(r'^update_user/',views.update_user),
    re_path(r'^add_user/',views.add_user),
    re_path(r'^delete_user/',views.delete_user),
]

views.py里定义四个视图函数

def get_user(request):
    return HttpResponse('')

def update_user(request):
    return HttpResponse('')

def add_user(request):
    return HttpResponse('')

def delete_user(request):
    return HttpResponse('')
```

- 这样会导致 url 越来越多，如果是对十张表crud 就会出现40个url
- 渐渐的出现了一个潜规则 —— 根据请求方法来区分 这样只写一个url就可以了

> #### restful 风格的接口开发

- 根据 method 不同做不同的操作

```
urlpatterns = [
    re_path(r'^user/',views.user),
]

views.py 里只写一个视图函数

def user(request):
    method = request.method
    if method == 'GET':
        return HttpResponse('')
    elif method == 'POST':
        return HttpResponse('')
    elif method == 'PUT':
        return HttpResponse('')
    elif method == 'DELETE':
        return HttpResponse('')
```

> #### 基于CBV的 restful 规范开发接口

```
urls.py

urlpatterns = [
    re_path(r'^students/', views.StudentsView.as_view()),
]

views.py

class StudentsView(View):
    def get(self,request,*args,**kwargs):
        return HttpResponse('get');

    def post(self,request,*args,**kwargs):
        return HttpResponse('post');

    def put(self,request,*args,**kwargs):
        return HttpResponse('put');

    def delete(self,request,*args,**kwargs):
        return HttpResponse('delete');

```
