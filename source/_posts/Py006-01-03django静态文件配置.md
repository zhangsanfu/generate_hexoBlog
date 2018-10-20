---
title: Py006-01-03django静态文件配置
date: 2018-10-21 00:01:48
tags: M06
---

### django静态文件处理之引入jquery

```
1.新建django项目   mydj002
2.设置more settings 里  在application name里输入  app001
3.继续实现timer的效果
    app001/views.py里
    from django.shortcuts import render
    # Create your views here.

    def timer(request):
        import time
        ctime = time.time()

        return render(request,"timer.html",{"ctime":ctime})

4. mydj002/urls.py里

    from django.contrib import admin
    from django.urls import path

    from app001 import views

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('timer/', views.timer)
    ]

5. templates里 放置一个jquery-3.1.1.js
    timer.html内容如下
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        h4{color:red;}
    </style>
</head>
<body>
    <script src="jquery-3.1.1.js"></script>
    <h4>当前时间:{{ ctime }}</h4>
    <script>
        $('h4').on('click',function(){
            $(this).css('color','green')
        })
    </script>
</body>
</html>

6.此时你单独运行 timer.py发现  
    ctimer没有正常渲染因为脱离了django单独运行 
    h4点击后确实变绿了 

7.但是你 python3 manage.py runserver   访问 localhost:8000/timer发现jquery并没有加载
```

> 为什么jquery没有加载

```
因为<script src="jquery-3.1.1.js"></script>里的路径不对
你需要对静态文件进行路径配置才可以访问
```

> #### 配置静态文件路径

```
1. 在项目里mydj002下新建 statics目录 把jquery-3.1.1.js移到这里
    此时你去浏览器里访问 localhost:8000/statics/jquery-3.1.1.js 还是报错
    因为你缺少一个配置
    因为你缺少一个配置
    因为你缺少一个配置

2. 在 mydj002/settings.py里 
    STATIC_URL = '/static/'
    # 添加如下内容
    # 注意必须叫 STATICFILES_DIRS这是规定好的
    # statics
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR,"statics")
    ]

    # STATIC_URL 是静态资源目录的一个别名   你访问时候就是 localhost:8000/static/静态文件
    # os.path.join(BASE_DIR,"statics") 中的 statics是你刚刚新建的statics目录名

3. localhost:8000/static/jquery-3.1.1.js 发现找到了


4. 在 timer.html里修改 jquery的路径 注意别名
    settings.py 里的 别名STATIC_URL = '/static/'
    记住你刚刚配置了别名 static
    记住你刚刚配置了别名 static
    记住你刚刚配置了别名 static
    <script src="/static/jquery-3.1.1.js"></script>

5. 访问 http://localhost:8000/timer/ 点击h4发现成功了！！！
```