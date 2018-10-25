---
title: Py006-01-09django之模版标签
date: 2018-10-25 21:22:05
tags: M06
---

### 标签

```
标签看起来像是这样的： {% tag %}。标签比变量更加复杂：一些在输出中创建文本，一些通过循环或逻辑来控制流程，一些加载其后的变量将使用到的额外信息到模版中。一些标签需要开始和结束标签 （例如{% tag %} ...标签 内容 ... {% endtag %}）。
```

#### for标签

- 遍历每一个元素：

```
num_list = [1,2,3]
{% for i in num_list %}
    <p>{{ i }}</p>
{% endfor %}


person_list = [{"name":1},{"name":2}]
{% for person in person_list %}
    <p>{{ person.name }}</p>
{% endfor %}
```

```
可以利用{% for obj in list reversed %}反向完成循环。
```

- 遍历一个字典：

```
{% for key,val in dic.items %}
    <p>{{ key }}:{{ val }}</p>
{% endfor %}

注：循环序号可以通过｛｛forloop｝｝显示　　
```

```
forloop.counter            The current iteration of the loop (1-indexed)
forloop.counter0           The current iteration of the loop (0-indexed)
forloop.revcounter         The number of iterations from the end of the loop (1-indexed)
forloop.revcounter0        The number of iterations from the end of the loop (0-indexed)
forloop.first              True if this is the first time through the loop
forloop.last               True if this is the last time through the loop
```

> for ... empty

```
for 标签带有一个可选的{% empty %} 从句，以便在给出的组是空的或者没有被找到时，可以有所操作。
```

```
arr = []
{% for i in arr %}
    <p>{{ i }}</p>

{% empty %}
    <p>暂无数据</p>
{% endfor %}
```

#### if 标签

```
{% if %}会对一个变量求值，如果它的值是“True”（存在、不为空、且不是boolean类型的false值），对应的内容块会输出。
```

```
{% if num > 100 or num < 0 %}
    <p>无效</p>
{% elif num > 80 and num < 100 %}
    <p>优秀</p>
{% else %}
    <p>凑活吧</p>
{% endif %}
```

#### with

使用一个简单地名字缓存一个复杂的变量，当你需要使用一个“昂贵的”方法（比如访问数据库）很多次的时候是非常有用的

例如：

```
person_list = [{"name":"aaa"}]
{% with person_list.1.name as n %}
    {{ n }}  ===> "aaa"
{% endwith %}
```

#### csrf_token

这个标签用于跨站请求伪造保护

```
之前post请求出现403问题 
之前是修改settings里的 csrf注释掉
```

> 这个标签是另一种方案

index.html添加这个标签就不会报错了

```
<form action="" method="post">
    {{ csrf_token }}
    账号<input type="text" name="user">
    密码<input type="text" pwd="pwd">
    <input type="submit">
</form>
```
