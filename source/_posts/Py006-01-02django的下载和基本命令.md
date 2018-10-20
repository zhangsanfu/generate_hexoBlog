---
title: Py006-01-02django的下载和基本命令
date: 2018-10-20 23:24:52
tags: M06
---

### 下载Django

```
pip install django

# 指定版本  我的例子都是基于2.x.x版本以上的
pip install django==2.0.1
```

查看django下载到哪里去了？

```
# step1 命令行运行
python
# step2
import sys
# step3
sys.path
# step4 把python的路径复制
# step5 退出 python命令行环境  exit
# step6
cd  你复制的路径
# step7 查看路径下的文件
ls
# step8 打开你安装的python版本的文件
open 3.6
# step9 点开bin文件 看看是否有django-admin文件
```

### 创建一个django project

```
# 假设你现在的命令行路径是桌面
django-admin.py startproject mysite
# 这样你就创建了一个如下目录的 mysite工程
mysite
|---manage.py
|---mysite
    |---__init__.py
    |---settings.py
    |---urls.py
    |---wsgi.py
```

- manage.py ----- Django项目里面的工具，通过它可以调用django shell和数据库等。
- settings.py ---- 包含了项目的默认设置，包括数据库信息，调试标志以及其他一些工作的变量。
- urls.py ----- 负责把URL模式映射到应用程序。

#### 在mysite目录下创建应用

```
# 切换到mysite目录
cd mysite 
# 创建一个新的应用
python manage.py startapp blog

# 你会发现生成了一个blog目录

blog
|---__init__.py
|---admin.py
|---apps.py
|---migrations
    |---__init__.py
|---models.py
|---tests.py
|---views.py
```

此阶段我们只需要关注两个文件

- models.py 跟数据库打交道
- views.py 跟视图函数打交道


#### 启动项目

```
python3 manage.py runserver
# 或者
python3 manage.py runserver 8888
```

打开浏览器输入[localhost:8888]你就看到小火箭了～～～


### 通过pycharm创建项目

```
1.直接新建项目选 django:输入 mydj001

2. 点击更多设置 more settings

3. application name里输入一个应用名称如： app001

4. 点击create
```

> #### 完成一个 localhost:8888/timer 的页面现实当前时间戳

第一步：按照路由规则写一个timer路由

```
# 修改urls.py
from django.contrib import admin
from django.urls import path

def timer(request):
    return

urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', timer)
]
```

第二步 但是这样显然很耦合——修改如下 

```
# 将timer函数定义在 app001/views.py里
from django.contrib import admin
from django.urls import path

from app001 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', views.timer)
]

```

第三步 

```
在templates目录新建timer.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h4>当前时间:</h4>
</body>
</html>
```

第四步 app001/views.py

```
from django.shortcuts import render

# Create your views here.

def timer(request):
    import time
    ctime = time.time()
    # django会自动去template目录里找timer.htmk
    return render(request,"timer.html")  
```

第五步 运行 python3 manage.py runserver

```
默认是 localhost:8000你访问就会报错

因为路由里只匹配了两个
urlpatterns = [
    path('admin/', admin.site.urls),
    path('timer/', views.timer)
]

你应该访问  localhost:8000/timer

# 但是没有具体的值
```

第六步 处理数据传递

```
# 修改app001/views.py
def timer(request):
    import time
    ctime = time.time()

    return render(request,"timer.html",{"ctime":ctime})


# 修改templates/timer.html
<h4>当前时间:{{ ctime }}</h4>


# 在pycharm里修改django项目不用重新启动就能更新
打开 localhost:8000/timer 目标完成！！！
```

