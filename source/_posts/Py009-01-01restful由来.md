---
title: Py009-01-01restful由来
date: 2019-02-07 15:32:41
tags: M09
---

> ### 1.开发模式

- 普通开发模式(前后端放在一起写)
- 前后端分离

> ### 2.后端开发

- 为前端提供URL(API/接口开发）
- 永远返回 HttpResponse

> ### 3.Django 中的 FBV/CBV

- FBV(function base view)
- CBV(class base view)

> 实践

```
# step001 新建 django项目 dj_restful    
# app 名为 app001 

# step002 修改 urls.py

from app001 import views;
urlpatterns = [
    re_path(r'^users/',views.users)
]

# step003 修改 app001/views.py

import json
from django.shortcuts import render,HttpResponse

def users(request):
    user_list = ['alex','aaa','bbb'];
    return HttpResponse(json.dumps(user_list));


# step004 启动 django 项目
访问 http://localhost:8000/users/

# 返回 
["alex","aaa","bbb"]

# 这就是你的第一个接口
```

> #### FBV 函数作为视图

```
def users(request):
    user_list = ['alex','aaa','bbb'];
    return HttpResponse(json.dumps(user_list));
```

> #### CBV 类作为视图

假如写个学生信息接口

```
# 第一步 修改 urls.py
from django.urls import path,re_path
from app001 import views;

urlpatterns = [
    re_path(r'^students/', views.StudentsView.as_view())
]

# 第二步 继承 View
from django.views import  View;
class StudentsView(View):
    def get(self,request,*args,**kwargs):
        return HttpResponse('get');

    def post(self,request,*args,**kwargs):
        return HttpResponse('post');

    def put(self,request,*args,**kwargs):
        return HttpResponse('put');

    def delete(self,request,*args,**kwargs):
        return HttpResponse('delete');

# 第三步 注释掉 settings.py 里的 csrf验证

# 第四步 安装 postman 
对 http://localhost:8000/students/ 发起 get/post/put/delete请求
```

> ### 4.列表生成式

```
class Foo:
    pass

class Bar:
    pass

v = [item() for item in [Foo,Bar]]
'''
相当于
v = [];
for i in [Foo,Bar]:
    obj = i() # 循环时Foo(), Bar() 实例对象
    v.append(obj)

v # 对象列表
'''
```

> ### 5.面向对象

- 封装
    - 对同一类方法封装到类中
        ```
        class File:
            # 文件crud方法
            def get ...
            def update ...
        
        class DB:
            #  sql crud方法
            def get ...
            def update ...
        ```
    - 将数据封装到对象中
        ```
        class Student:
            def __init__(self,name,age):
                self.name = name
                self.age = age

        stu1 = Student('Alex',18)
        ```

> ### 6.封装示例

```
class Request():
    def __init__(self,obj):
        self.obj = obj;

    @property
    def user(self):
        return self.obj.authticate()

class Auth():
    def __init__(self,name,age):
        self.name = name
        self.age = age
    
    def authticate(self):
        return self.name

# 接口 class
class APIView():
    # 派发事件
    def dispatch(self):
        # 利用反射   get/post/put/delete
        self.get();


    def get(self):
        a = Auth('alex',18)
        req = Request(a);
        print(req.user)

obj = APIView()
obj.dispatch(); # 返回 alex
```
