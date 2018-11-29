---
title: Py007-01-04requests模块
date: 2018-11-29 23:09:15
tags: M07
---

### requests模块

- 什么是requests模块

requests模块是python中原生的基于网络请求的模块，其主要作用是用来模拟浏览器发起请求。功能强大，用法简洁高效。在爬虫领域中占据着半壁江山的地位。

- 为什么要使用requests模块

```
> 因为在使用urllib模块的时候，会有诸多不便之处，总结如下：
    1 手动处理url编码
    2 手动处理post请求参数
    3 处理cookie和代理操作繁琐
        > cookie
        - 创建一个cookiejar对象
        - 创建一个handler对象
        - 创建一个opener对象

        > 代理
        - 创建handler对象，代理ip和端口封装到该对象
        - 创建opener对象

> 使用requests模块：
    - 自动处理url编码
    - 自动处理post请求参数
    - 大大简化cookie和代理操作
    。。。
```

#### requests如何使用

```
# 安装
pip install requests 
```

> 使用流程

```
1. 指定url
2. 使用requests模块发请求
3. 获取响应数据
4. 进行持久化存储
```

#### 通过5个基于requests模块的爬虫项目对该模块进行系统的学习和巩固

- get
- post
- ajax的get
- ajax的post
- 综合

#### get请求

```
import requests

# 指定url
url = 'https://www.sogou.com/'

# 发get请求,get方法会返回请求成功的响应内容
response = requests.get(url=url)

# 获取响应中的数据值：text可以获取响应对象中字符串形式的页面数据
page_data = response.text

print(page_data)

# 持久化存储
with open('./sogou.html','w',encoding='utf-8') as fp:
    fp.write(page_data)
```

#### requests其他常用属性

```
import requests

url = 'https://www.sogou.com/'

response = requests.get(url=url)

page_data = response.text
# 获取响应对象中二进制(byte)类型的数据
print(response.content)

# 返回响应的状态码 
print(response.status_code)

# 获取响应的头信息(字典的形式)
print(response.headers)

# 获取请求的url
print(response.url)
```

#### request携带参数的get请求方式一

> 获取搜狗搜索结果对应的界面数据爬取 

获取周杰伦对应的搜狗页面

```
import requests

url = 'https://www.sogou.com/web?query=周杰伦&ie=utf-8'

response = requests.get(url=url)

page_text = response.text

with open('./zhou.html','w',encoding='utf-8') as f:
    f.write(page_text)
```

此时url里的中文参数无需手动处理(urllib需要手动处理)

#### request携带参数的get请求方式二

- params参数(字典的形势传递get参数)

```
import requests

# get请求方式二
url = 'https://www.sogou.com/web'

# 将参数封装到字典里
params = {
    'query':'周杰伦',
    'ie':'utf-8'
}

response = requests.get(url=url,params=params)

page_text = response.text

print(page_text)
```

#### requests之自定义请求头信息

- headers 请求头参数

```
import requests

url = 'https://www.sogou.com/'

#自定义请求头信息
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}

# 将参数封装到字典里
params = {
    'query':'周杰伦',
    'ie':'utf-8'
}

response = requests.get(url=url,params=params,headers=headers)

print(response.text)
```



