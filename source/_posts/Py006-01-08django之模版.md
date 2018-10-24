---
title: Py006-01-08django之模版
date: 2018-10-24 21:56:52
tags: M06
---

> 请自行建立一个django项目

- 一个视图函数 index 渲染 render(request,'index.html')

### 模版语法



```
- 变量：{{}} 双花括号
    1深度查询   句点符 .
- 标签 {% %}
```

```
def index(request):
    name = 'hjx'
    age = 18
    isMan = True
    num_list = [11, 22, 33]
    info = {"name": "hjx", "age": 18}

    class Person(object):
        def __init__(self, name, age):
            self.name = name
            self.age = age

    aa = Person("hjx", 18)
    bb = Person("alex", 98)
    person_list = [aa, bb]
    # 这样写每个值都要写一遍很麻烦
    # return render(request,"index.html",{"name":name,"age":age,"isMan":isMan,"num_list":num_list,"info":info,"aa":aa,"bb":bb,"person_list":person_list})
    # 使用locals
    return render(request,'index.html',locals())
```

index.html里取值

```
<p>{{ name }}</p>
<p>{{ age }}</p>
<p>{{ isMan }}</p>
<p>{{ num_list }}</p>
<p>{{ info }}</p>
<p>{{ aa }}</p>
<p>{{ bb }}</p>
<p>{{ person_list }}</p>

<hr>
深度查询
<p>{{ num_list.1 }}</p>
<p>{{ info.name }}</p>
<p>{{ aa.name }}</p>
<p>{{ bb.age }}</p>
<p>{{ person_list.1.name }}</p>
```

### 过滤器

语法规则

```
{{变量|过滤器名字:param}}
```

```
def index(request):
    import datetime
    now = datetime.now()
    arr = []
    return render(request,'index.html',locals())

```

> index.html

```
{{now|date:"Y-m-d"}}   ===> "2018-10-11"
{{arr|default:"数据为空"}} ===> "数据为空"
{{arr|length}}  ===> 0
```

> default

如果一个变量是false或者为空，使用给定的默认值。否则，使用变量的值。例如：

```
{{ value|default:"nothing" }}
```

> length

返回值的长度。它对字符串和列表都起作用。例如：

```
{{ value|length }}
如果 value 是 ['a', 'b', 'c', 'd']，那么输出是 4。
```

> filesizeformat

将值格式化为一个 “人类可读的” 文件尺寸 （例如 '13 KB', '4.1 MB', '102 bytes', 等等）。例如：

```
{{ value|filesizeformat }}
如果 value 是 123456789，输出将会是 117.7 MB。
```

> date

如果 value=datetime.datetime.now()

```
{{ value|date:"Y-m-d" }}　
```

> slice

如果 value="hello world"

```
{{ value|slice:"2:-1" }}
```

> truncatechars

如果字符串字符多于指定的字符数量，那么会被截断。截断的字符串将以可翻译的省略号序列（“...”）结尾。

参数：要截断的字符数

例如：

```
{{ value|truncatechars:9 }}
```

> truncatewords 按照单词截

> safe

```
value="<a href="">点击</a>"
{{value}}  ===> 此时渲染出来不是a标签 
```

Django的模板中会对HTML标签和JS等语法标签进行自动转义，原因显而易见，这样是为了安全。但是有的时候我们可能不希望这些HTML元素被转义，比如我们做一个内容管理系统，后台添加的文章中是经过修饰的，这些修饰可能是通过一个类似于FCKeditor编辑加注了HTML修饰符的文本，如果自动转义的话显示的就是保护HTML标签的源文件。为了在Django中关闭HTML的自动转义有两种方式，如果是一个单独的变量我们可以通过过滤器“|safe”的方式告诉Django这段代码是安全的不必转义。比如：

```
value="<a href="">点击</a>"
{{ value|safe}}

```

> add

```
value = 1
{{value|add:100}}
```

> upper

```
value = "a"
{{value|upper}} ===> A 
```