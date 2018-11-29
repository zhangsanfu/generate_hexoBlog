---
title: Py007-01-05requests模块之post和ajax
date: 2018-11-29 23:52:05
tags: M07
---

### requests模块之post请求

> 简单需求，登录豆瓣网，获取成功后的数据

```
import requests

# 1.post请求的url
url = 'https://accounts.douban.com/login'

# 封装post请求参数数据
data = {
    "source":'movie',
    'redir':'https://movie.douban.com/',
    'form_email':'15027900535',
    'form_password':'bobo@15027900535',
    'login':'登录'
}

#自定义请求头信息
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}
# 2 发起post请求
response = requests.post(url=url,data=data,headers=headers)

# 3 获取响应对象中的页面数据
page_text = response.text

# 4 持久化存储
with open('movie.html','w',encoding='utf-8') as f:
    f.write(page_text)
```

### ajax的请求

#### 基于ajax的get请求

> 抓取豆瓣电影首页-排行榜-某一分类的电影的下拉加载数据

- 排行榜 https://movie.douban.com/chart
- 喜剧分类 https://movie.douban.com/typerank?type_name=%E5%96%9C%E5%89%A7&type=24&interval_id=100:90&action=
- 下拉加载的动态数据接口 https://movie.douban.com/j/chart/top_list?type=24&interval_id=100%3A90&action=&start=40&limit=20


```
import requests

# 原始url
# baseurl = 'https://movie.douban.com/j/chart/top_list?type=24&interval_id=100%3A90&action=&start=40&limit=20'

# 指定url
url = 'https://movie.douban.com/j/chart/top_list'

# 封装ajax中  get参数
params = {
    'type':24,
    'interval_id':'100:90',
    'action':'',
    'start':40,
    'limit':20
}

# 自定义请求头信息
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}

response = requests.get(url=url,params=params,headers=headers)

print(response.text)
```

#### 基于ajax的post请求

获取肯德基城市餐厅位置数据

- 肯德基官网 http://www.kfc.com.cn/kfccda/index.aspx
- 餐厅位置查询页面 http://www.kfc.com.cn/kfccda/storelist/index.aspx
- 搜索查询 北京 餐厅列表  看浏览器里的network里的抓包信息 获得url = http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword
- 餐厅列表有分页但是是  post的 继续分析他的请求参数
  
```
http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword

参数
cname: 
pid: 
keyword: 上海
pageIndex: 2
pageSize: 10
```

```
import requests

url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'


# 封装ajax中  post参数
data = {
    'cname':'',
    'pid':'',
    'keyword': '上海',
    'pageIndex':2,
    'pageSize':10
}

#自定义请求头信息
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}

response = requests.post(url=url,data=data,headers=headers)

print(response.text)
```

> 结论

- 如果使用requests发起ajax请求 跟普通get/post请求是一样的
- 无法通过地址栏请求，通过浏览器开发者工具抓包信息获取请求的url