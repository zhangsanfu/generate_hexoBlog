---
title: Py006-01-07django之request和response
date: 2018-10-24 21:03:34
tags: M06
---

### 请求对象

> 前置工作准备项目

```
# step001
新建django项目  mydj004 设置应用名  app001
# step002 修改 mydj004/urls.py
from django.contrib import admin
from django.urls import path,re_path

from app001 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    re_path(r'index/',views.index)
]

# step003 app001/views.py
from django.shortcuts import render,HttpResponse

def index(request):
    # 此处逻辑处理操作请求对象 request
    
    return render(request,"index.html")

# step004 template/index.html

<form action="" method="post">
    账号<input type="text" name="user">
    密码<input type="text" pwd="pwd">
    <input type="submit">
</form>

action什么都不写代表会拿当前url作为路径访问


# step005 去掉settings.py里的 csrf设置

 'django.middleware.csrf.CsrfViewMiddleware', 注释就好

# step006 打开 http://localhost:8000/index/ 准备工作完毕
```

#### request的一些内容

在视图函数里修改就可以了。。。

- method
- GET 将get请求参数封装在字典里
- POST 将post请求参数封装在字典里
- path 返回请求url的路径

```
def index(request):
    print('method', request.method)  # 打印请求的方式
    print(request.GET)  # 访问 http://localhost:8000/index/?name=1&pwd=2&name=22  {'name': ['1', '22'], 'pwd': ['2']}>
    print(request.POST) # http://localhost:8000/index/  提交表单内容 name=1 pwd=2  打印 {'name': ['1'], 'pwd': ['2']}>
    # 取字典的值
    name = request.GET.get("name")
    # 请求路径
    '''
    http://localhost:8000/index/?name=1&pwd=2
    url:协议://ip:port/路径?get请求数据
    '''
    print(request.path)
    
    return render(request,"index.html")
```

> 如果此时匹配 http://localhost:8000时访问  index.html

```
mydj004/urls.py里添加一个匹配规则
re_path(r'^$',views.index) 


# 此时 request.path打印  /
```

#### request常用方法

- get_full_path() 跟path不同的是  同时携带get请求数据
- is_ajax() 以后详细说 就是判断是不是ajax请求

```
# http://localhost:8000/index/?name=1&pwd=2
request.path ===> /index/
request.get_full_path() ===> /index/?name=1&pwd=2

```

### request响应对象

对应视图函数里的 return

- HttpResponse
- render

```
def index(request):
    return 响应内容

# 返回字符串  "OK"
return HttpResponse("OK")

# 返回h1标签
return HttpResponse("<h1>OK</h1>")


# render返回 templates里的页面
return render(request,"index.html")

# 可选参数 渲染变量 将视图函数里的数据渲染到页面里这时就要传递这个参数
import time
ctime = time.time()
return render(request,"index.html",{"timer":ctime})

修改index.html 添加个p标签
<p>{{ timer }}</p>
```

[参考](https://www.cnblogs.com/yuanchenqi/articles/8876856.html)

