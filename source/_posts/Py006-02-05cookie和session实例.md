---
title: Py006-02-05cookie和session实例
date: 2018-11-13 21:44:31
tags: M06
---

> 详情请看我之前的知识总结

[cookie](https://sltrust.github.io/2018/02/09/N043_Cookie/)

[session](https://sltrust.github.io/2018/02/10/N044__01_Session/)

> ### 前置准备

models.py

```
from django.db import models
class UserInfo(models.Model):
    user = models.CharField(max_length=32)
    pwd = models.CharField(max_length=32)
```

urls.py

```
from django.contrib import admin
from django.urls import path
from app01 import views
urlpatterns = [
    path('admin/', admin.site.urls),
    path('login/',views.login),
    path('index/',views.index)
]
```

login.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="" method="post">
    {% csrf_token %}
    <p>
    用户名<input type="text" id="user" name="user">
    </p>
    <p>
        密码<input type="text" id="pwd" name="pwd">
    </p>
    <p>
        <input type="submit" value="提交">
    </p>
</form>
</body>
</html>
```

### django中使用cookie

![](https://raw.githubusercontent.com/slTrust/note/master/img/py/py006_02_0501.png)

> 设置cookie：

```
rep = HttpResponse(...) 或 rep ＝ render(request, ...) 或 rep ＝ redirect()  
rep.set_cookie(key,value,...)
rep.set_signed_cookie(key,value,salt='加密盐',...)
```

源码

```
class HttpResponseBase:
        def set_cookie(self, key,                 键
        　　　　　　　　　　　　 value='',            值
        　　　　　　　　　　　　 max_age=None,        超长时间
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　cookie需要延续的时间（以秒为单位）
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　如果参数是\ None`` ，这个cookie会延续到浏览器关闭为止。
        　　　　　　　　　　　　 expires=None,        超长时间
       　　　　　　　　　　　　　　　　　　　　　　　　　　expires默认None ,cookie失效的实际日期/时间。
    　　　　　　　　　　　　　　　　　　　　　　　　　　　　
        　　　　　　　　　　　　 path='/',           Cookie生效的路径，
                                                 浏览器只会把cookie回传给带有该路径的页面，这样可以避免将
                                                 cookie传给站点中的其他的应用。
                                                 / 表示根路径，特殊的：根路径的cookie可以被任何url的页面访问
        　　　　　　　　　　　　
                             domain=None,         Cookie生效的域名

                                                  你可用这个参数来构造一个跨站cookie。
                                                  如， domain=".example.com"
                                                  所构造的cookie对下面这些站点都是可读的：
                                                  www.example.com 、 www2.example.com
        　　　　　　　　　　　　　　　　　　　　　　　　　和an.other.sub.domain.example.com 。
                                                  如果该参数设置为 None ，cookie只能由设置它的站点读取。
        　　　　　　　　　　　　 secure=False,        如果设置为 True ，浏览器将通过HTTPS来回传cookie。
        　　　　　　　　　　　　 httponly=False       只能http协议传输，无法被JavaScript获取
                                                 （不是绝对，底层抓包可以获取到也可以被覆盖）
        　　　　　　　　　　): pass
```

> 获取cookie：

```
request.COOKIES
```

> 删除cookie：

```
response.delete_cookie("cookie_key",path="/",domain=name)
```

#### 需求登陆的时候设置cookie,存储登陆状态和用户名，进入index的时候显示欢迎，用户xxx

```
数据库迁徙别忘了执行这个
python3 manage.py makemigrations
python3 manage.py migrate
```

views.py

```
from django.shortcuts import render,HttpResponse,redirect
from app01.models import UserInfo
# 自行插入两条用户数据
# user:hjx  pwd: 111
# user:aaa  pwd: 111

def login(request):
    if request.method=='POST':
        user = request.POST.get('user')
        pwd = request.POST.get('pwd')
        u = UserInfo.objects.filter(user=user,pwd=pwd).first()

        if u:
            # 登陆成功
            '''
            响应体有三种
            return HttpResponse
            return redirect()
            return render()
            
            必须通过响应体设置cookie
            '''
            response = HttpResponse('登陆成功')
            response.set_cookie('is_login',True)
            response.set_cookie('user_name', u.user)
            return response

        else:
            # 失败
            pass

    return render(request,'login.html')


# 如果已经登陆了  进入首页显示对应的用户名  反之进入登陆页
def index(request):
    print(request.COOKIES)

    is_login = request.COOKIES.get('is_login')

    if is_login:
        username = request.COOKIES.get('user_name')
        return render(request,'index.html',{'user_name':username})
    else:
        return redirect('/login/')
```

#### cookie的参数

> 设置cookie失效时间

- max_age 毫秒值(靠谱)
- expires 指定某个时刻(不靠谱，客户端设置2099年)

```
response.set_cookie('is_login',True,max_age=15)
response.set_cookie('user_name', u.user)

# is_login 15秒后失效
# user_name不会15秒后失效  只要浏览器不关就会一直存在

# -------------------------
# 另一种时间设置   日期类型
# 过期时间
import datetime
date = datetime.datetime(year=2020,month=5,day=1,hour=14,minute=32,second=10)
response.set_cookie('xxx','111',expires=date)
```

> 有效路径设置

- path 默认 '/'就是根目录下所有路径都可以使用

```
response.set_cookie('user_name', u.user,path='/index/')
# 此时只有 路由index下的可以访问cookie  user_name
```


### django中使用session

Session是服务器端技术，利用这个技术，服务器在运行时可以 为每一个用户的浏览器创建一个其独享的session对象，由于 session为用户浏览器独享，所以用户在访问服务器的web资源时 ，可以把各自的数据放在各自的session中，当用户再去访问该服务器中的其它web资源时，其它web资源再从用户各自的session中 取出数据为用户服务

![](https://raw.githubusercontent.com/slTrust/note/master/img/py/py006_02_0502.png)

> django中session语法

```
1、设置Sessions值
          request.session['session_name'] ="admin"
2、获取Sessions值
          session_name = request.session["session_name"]
3、删除Sessions值
          del request.session["session_name"]
4、flush()
     删除当前的会话数据并删除会话的Cookie。
     这用于确保前面的会话数据不可以再次被用户的浏览器访问
5、get(key, default=None)
  fav_color = request.session.get('fav_color', 'red')  
6、pop(key)
  fav_color = request.session.pop('fav_color')  
7、keys()
8、items()  
9、setdefault()  
10 用户session的随机字符串
        request.session.session_key

        # 将所有Session失效日期小于当前日期的数据删除
        request.session.clear_expired()

        # 检查 用户session的随机字符串 在数据库中是否
        request.session.exists("session_key")

        # 删除当前用户的所有Session数据
        request.session.delete("session_key")

        request.session.set_expiry(value)
            * 如果value是个整数，session会在些秒数后失效。
            * 如果value是个datatime或timedelta，session就会在这个时间后失效。
            * 如果value是0,用户关闭浏览器session就会失效。
            * 如果value是None,session会依赖全局session失效策略。
```

#### session 登陆实例

views.py

```
def login_session(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")

        user = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if user:
            import datetime

            now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            # 设置session
            request.session["is_login"] = True
            request.session["username"] = user.user
            request.session["last_visit_time"] = now

            '''
            if request.COOKIE.get("sessionid"):
                更新

                 在django—session表中创建一条记录:
                   session-key                                     session-data
                   ltv8zy1kh5lxj1if1fcs2pqwodumr45t                  更新数据
            else:
                1 生成随机字符串   ltv8zy1kh5lxj1if1fcs2pqwodumr45t
                2 response.set_cookie("sessionid",ltv8zy1kh5lxj1if1fcs2pqwodumr45t)
                3 在django—session表中创建一条记录:
                   session-key                                     session-data
                   ltv8zy1kh5lxj1if1fcs2pqwodumr45t       {"is_login":True,"username":"yuan"}

            '''
            return HttpResponse("登录成功!")

    return render(request, "login.html")


def index_session(request):
    print("is_login:", request.session.get("is_login"))

    '''
    1  request.COOKIE.get("session")  #  ltv8zy1kh5lxj1if1fcs2pqwodumr45t
    2  django-session表中过滤纪录:
       在django—session表中创建一条记录:
               session-key                                   session-data
               ltv8zy1kh5lxj1if1fcs2pqwodumr45t       {"is_login":True,"username":"yuan"}

       obj=django—session.objects .filter(session-key=ltv8zy1kh5lxj1if1fcs2pqwodumr45t).first()

    3 obj.session-data.get("is_login")
    '''
    is_login = request.session.get("is_login")
    if not is_login:
        return redirect("/login_session/")

    username = request.session.get("username")
    last_visit_time = request.session.get("last_visit_time")

    return render(request, "index.html", {"username": username, "last_visit_time": last_visit_time})

# 删除session
def logout(request):
    # del request.session["is_login"]

    request.session.flush()

    '''
    1 randon_str=request.COOKIE.get("sessionid")
    # 删除整条session记录
    2 django-session.objects.filter(session-key=randon_str).delete()
    # 同时删除cookie记录 那个sessionid
    3 response.delete_cookie("sessionid",randon_str)

    '''

    return redirect("/login/")

```

#### session配置

```
Django默认支持Session，并且默认是将Session数据存储在数据库中，即：django_session 表中。

a. 配置 settings.py

    SESSION_ENGINE = 'django.contrib.sessions.backends.db'   # 引擎（默认）

    SESSION_COOKIE_NAME ＝ "sessionid"                       # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串（默认）
    SESSION_COOKIE_PATH ＝ "/"                               # Session的cookie保存的路径（默认）
    SESSION_COOKIE_DOMAIN = None                             # Session的cookie保存的域名（默认）
    SESSION_COOKIE_SECURE = False                            # 是否Https传输cookie（默认）
    SESSION_COOKIE_HTTPONLY = True                           # 是否Session的cookie只支持http传输（默认）
    SESSION_COOKIE_AGE = 2*7*24*3600                             # Session的cookie失效日期（2周）（默认）
    SESSION_EXPIRE_AT_BROWSER_CLOSE = False                  # 是否关闭浏览器使得Session过期（默认）
    SESSION_SAVE_EVERY_REQUEST = False                       # 是否每次请求都保存Session，默认修改之后才保存（默认）


# ------------------
SESSION_COOKIE_AGE = 2*7*24*3600  # 2周后过期
'''
假如2018-01-01登陆  15号失效
如果你设置了
SESSION_SAVE_EVERY_REQUEST = True  
此时你 在 2018-01-02登陆了  则导致  16号失效
此时你 在 2018-01-03登陆了  则导致  17号失效

'''
```