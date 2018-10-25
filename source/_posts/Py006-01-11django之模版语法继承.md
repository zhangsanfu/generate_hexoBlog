---
title: Py006-01-11django之模版语法继承
date: 2018-10-25 22:09:02
tags: M06
---

### 模版继承

Django模版引擎中最强大也是最复杂的部分就是模版继承了。模版继承可以让您创建一个基本的“骨架”模版，它包含您站点中的全部元素，并且可以定义能够被子模版覆盖的 blocks 。

> 自行建立一个django项目 

- 含有应用 app001
- 视图函数 index
- 视图模版 index.html

app001/urls.py

```
from app001 import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path(r'index/',views.index)
]
```

app001/views.py

```
def index(request):
    return render(request,'index.html')
```

index.html有如下布局

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        header{height: 50px;background: #369;}
        .left{background: #ddd;}
        .right{background: aqua}
    </style>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<header></header>
<div class="container">
    <div class="row">
        <div class="col-md-3 left">
            <ol>
                <li>菜单1</li>
                <li>菜单2</li>
                <li>菜单3</li>
                <li>菜单4</li>
                <li>菜单5</li>
            </ol>
        </div>
        <div class="col-md-9 right">
            <ul>
                <li>1</li>
                <li>2</li>
                <li>3</li>
                <li>4</li>
                <li>5</li>
                <li>6</li>
                <li>7</li>
                <li>8</li>
                <li>9</li>
                <li>10</li>
            </ul>
        </div>
    </div>
</div>
</body>
</html>
```

> ### 需求想复用左侧的菜单列表

拷贝菜单这段代码，在templates下建立menu.html

```
<ol>
    <li>菜单1</li>
    <li>菜单2</li>
    <li>菜单3</li>
    <li>菜单4</li>
    <li>菜单5</li>
</ol>
```

> 修改index.html

```
<div class="col-md-3 left">
    <ol>
        <li>菜单1</li>
        <li>菜单2</li>
        <li>菜单3</li>
        <li>菜单4</li>
        <li>菜单5</li>
    </ol>
</div>
替换为
<div class="col-md-3 left">
    {% include 'menu.html' %}
</div>
```

> ### 需求现在有订单页order.html 想有index.html的 顶部的header和左边菜单

创建模版文件base.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        header{height: 50px;background: #369;}
        .left{background: #ddd;}
        .right{background: aqua}
    </style>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<header></header>
<div class="container">
    <div class="row">
        <div class="col-md-3 left">
            {% include 'menu.html' %}
        </div>
        <div class="col-md-9 right">
           {% block con %}
               <h1>默认显示内容</h1>
           {% endblock  %}
        </div>
    </div>
</div>
</body>
</html>

-----------------------------
{% block con %}
    
{% endblock  %}
是一个空盒子 等继承的人来填充内容

也可以设置默认内容
{% block con %}
    <h1>默认显示内容</h1>
{% endblock  %}
当继承模版的页面没有使用盒子就用这个默认的内容
使用了就覆盖掉这个默认的盒子
```

> index.html

- extends 标签是这里的关键。它告诉模版引擎，这个模版“继承”了另一个模版。
  
```
{% extends 'base.html' %}

{% block con %}
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
    <li>10</li>
</ul>
{% endblock %}
```

#### 注意事项

- 如果你在模版中使用 {% extends %} 标签，它必须是模版中的第一个标签。其他的任何情况下，模版继承都将无法工作。

- 在base模版中设置越多的 {% block %} 标签越好。请记住，子模版不必定义全部父模版中的blocks，所以，你可以在大多数blocks中填充合理的默认内容，然后，只定义你需要的那一个。多一点钩子总比少一点好。

- 如果你发现你自己在大量的模版中复制内容，那可能意味着你应该把内容移动到父模版中的一个 {% block %} 中。

- If you need to get the content of the block from the parent template, the {{ block.super }} variable will do the trick. This is useful if you want to add to the contents of a parent block instead of completely overriding it. Data inserted using {{ block.super }} will not be automatically escaped (see the next section), since it was already escaped, if necessary, in the parent template.

- 为了更好的可读性，你也可以给你的 {% endblock %} 标签一个 名字 。例如：

```
{% block content %}
...
{% endblock content %}　　
```

在大型模版中，这个方法帮你清楚的看到哪一个　 {% block %} 标签被关闭了。

- 不能在一个模版中定义多个相同名字的 block 标签。

