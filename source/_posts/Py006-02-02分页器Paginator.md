---
title: Py006-02-02分页器Paginator
date: 2018-11-05 22:21:05
tags: M06
---

### 前置准备

新建django项目应用app01

models.py

```
from django.db import models

# Create your models here.
class Book(models.Model):
    title = models.CharField(max_length=32)
    price = models.DecimalField(decimal_places=2,max_digits=8)
```

urls.py

```
from app01 import views
urlpatterns = [
    path('admin/', admin.site.urls),
    path('index/',views.index)
]
```

数据库迁徙

```
python3 manage.py makemigrations
python3 manage.py migrate
```


views.py

> 批量录入数据

```
# 录入初识数据100条
def index(request):

    # 缺陷  循环一次插入一条
    '''
    for i in range(100):
        Book.objects.create(title="book%s"%i,price=i*i)
    '''
    # 改善批量插入
    '''
    book_list = []
    for i in range(100):
        book = Book(title="book%s" % i, price=i * i)
        book_list.append(book)

    Book.objects.bulk_create(book_list) # 批量插入

    return render(request,'index.html',locals())
```

index.html

```
<ul>
    {% for book in book_list %}
        <li>{{ book.title }}:{{ book.price }}</li>
    {% endfor %}
</ul>
```

如上我们有了100条数据

### 分页器 Paginator

```
# 引入分页器
from django.core.paginator import Paginator

def index(request):
book_list = Book.objects.all()
    # 分页器 Paginator(列表,分页条数)
    paginator = Paginator(book_list,10)

    # 总数据条数
    print("count:",paginator.count)
    # 总页数
    print("num_pages:", paginator.num_pages)
    # 页码对应的列表
    print("page_range:", paginator.page_range)
    # 第x页数据
    page3 = paginator.page(3)
    '''
    或者迭代取第x页数据
    for i in page3
        print(i)
    '''
    print("第三页数据",page3.object_list)
    return render(request,'index.html',locals())

'''
访问 http://127.0.0.1:8000/index/
count: 100
num_pages: 10
page_range: range(1, 11)
第三页数据 <QuerySet [<Book: Book object (21)>, <Book: Book object (22)>, <Book: Book object (23)>, <Book: Book object (24)>, <Book: Book object (25)>, <Book: Book object (26)>, <Book: Book object (27)>, <Book: Book object (28)>, <Book: Book object (29)>, <Book: Book object (30)>]>
'''
```

> 通过url的get参数模拟动态页码

```
from django.shortcuts import render
from app01.models import Book

# 引入分页组件,和边界值异常类型处理边界值
from django.core.paginator import Paginator,EmptyPage

def index(request):

    #分页组件 初始化一定数量的数据

    # 缺陷  循环一次插入一条
    '''
    for i in range(100):
        Book.objects.create(title="book%s"%i,price=i*i)
    '''
    # 改善批量插入
    '''
    book_list = []
    for i in range(100):
        book = Book(title="book%s" % i, price=i * i)
        book_list.append(book)

    Book.objects.bulk_create(book_list) # 批量插入
    '''

    book_list = Book.objects.all()
    # 分页器 Paginator(列表,分页条数)
    paginator = Paginator(book_list,10)

    # 总数据条数
    # print("count:",paginator.count)
    # 总页数
    # print("num_pages:", paginator.num_pages)
    # 页码对应的列表
    # print("page_range:", paginator.page_range)
    # 第x页数据
    # page3 = paginator.page(3)
    # print("第三页数据",page3.object_list)

    try:
        current_page = int(request.GET.get('page', 1))
        current_page_data = paginator.page(current_page)
        # 处理边界值  <1,>10
    except EmptyPage as e:
        current_page_data = paginator.page(1)


    return render(request,'index.html',locals())
```

index.html

```
<ul>
    {% for book in current_page_data %}
        <li>{{ book.title }}:{{ book.price }}</li>
    {% endfor %}
</ul>
```

访问

```
http://127.0.0.1:8000/index/
http://127.0.0.1:8000/index/?page=1
http://127.0.0.1:8000/index/?page=2
http://127.0.0.1:8000/index/?page=10
http://127.0.0.1:8000/index/?page=2000
http://127.0.0.1:8000/index/?page=-1
```

#### 引入bootstrap分页组件

- paginator.page_range 分页列表

```
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">


<ul>
    {% for book in current_page_data %}
        <li>{{ book.title }}:{{ book.price }}</li>
    {% endfor %}
</ul>

<nav aria-label="Page navigation">
  <ul class="pagination">
    <li><a href="#" aria-label="Previous"><span aria-hidden="true">上一页</span></a></li>
    {% for index in  paginator.page_range %}
    <li><a href="#">{{ index }}</a></li>
    {% endfor %}
    <li><a href="#" aria-label="Next"><span aria-hidden="true">下一页</span></a></li>
  </ul>
</nav>
```

点击页码a标签跳转url设置当前页码数据

```
{% for index in  paginator.page_range %}
    {% if current_page == index %}
    <li class="active"><a href="?page={{ index }}">{{ index }}</a></li>
    {% else %}
    <li><a href="?page={{ index }}">{{ index }}</a></li>
    {% endif %}
{% endfor %}
```

> 上一页下一页处理


```
<li><a href="?page={{ current_page|add:-1 }}" aria-label="Previous"><span aria-hidden="true">上一页</span></a></li>

<li><a href="?page={{ current_page|add:1" aria-label="Next"><span aria-hidden="true">下一页</span></a></li>
```

> 通过分页器的上一页下一页api接口处理

- current_page_data.previous_page_number
- current_page_data.next_page_number

```
<li><a href="?page={{ current_page_data.previous_page_number }}" aria-label="Previous"><span aria-hidden="true">上一页</span></a></li>
    
<li><a href="?page={{ current_page_data.next_page_number }}" aria-label="Next"><span aria-hidden="true">下一页</span></a></li>
# 仍然有bug 在第2~9是好的  1和10是坏的
```

> 边界值处理

- current_page_data.has_previous
- current_page_data.has_next

```
{% if current_page_data.has_previous %}
    <li><a href="?page={{ current_page_data.previous_page_number }}" aria-label="Previous"><span aria-hidden="true">上一页</span></a></li>
{% else %}
    <li class="disabled"><a href="#" aria-label="Previous"><span aria-hidden="true">上一页</span></a></li>
{% endif  %}
...

{% if current_page_data.has_next %}
    <li><a href="?page={{ current_page_data.next_page_number }}" aria-label="Next"><span aria-hidden="true">下一页</span></a></li>
{% else %}
    <li class="disabled"><a href="#" aria-label="Next"><span aria-hidden="true">下一页</span></a></li>
{% endif  %}
```

#### 页码过多时的处理  

- 左5右5

总不能分页100页全显示出来吧！

```
# 特殊处理
from django.shortcuts import render
from app01.models import Book

# 引入分页组件
from django.core.paginator import Paginator,EmptyPage


def index(request):

    book_list = Book.objects.all()
    # 分页器 Paginator(列表,分页条数)
    paginator = Paginator(book_list,2)

    # 总数据条数
    print("count:",paginator.count)
    # 总页数
    print("num_pages:", paginator.num_pages)
    # 页码对应的列表
    print("page_range:", paginator.page_range)
    # 第x页数据
    # page3 = paginator.page(3)
    # print("第三页数据",page3.object_list)

    # 左五右五
    current_page = int(request.GET.get('page', 1))

    if paginator.num_pages > 11:

        if current_page - 5 < 1:
            page_range = range(1, 12)
        elif current_page + 5 > paginator.num_pages:
            page_range = range(paginator.num_pages - 10, paginator.num_pages + 1)

        else:
            page_range = range(current_page - 5, current_page + 6)
    else:
        page_range = paginator.page_range

    try:

        current_page_data = paginator.page(current_page)

    except EmptyPage as e:
        current_page_data = paginator.page(1)

    return render(request,'index.html',locals())
```

index.html里的页码循环部分替换页码列表page_range

```
{% for index in  page_range %}
    {% if current_page == index %}
    <li class="active"><a href="?page={{ index }}">{{ index }}</a></li>
    {% else %}
    <li><a href="?page={{ index }}">{{ index }}</a></li>
    {% endif %}
{% endfor %}
```

