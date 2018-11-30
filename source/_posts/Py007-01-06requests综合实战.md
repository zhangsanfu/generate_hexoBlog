---
title: Py007-01-06requests综合实战
date: 2018-11-30 21:08:18
tags: M07
---

### 综合项目实战

- 需求：爬取搜狗知乎某一个词条对应一定范围页码表示的页面数据

> 分析网址

```
# 原始url
baseUrl = 'https://zhihu.sogou.com/zhihu?query=%E9%92%89%E9%92%89&ie=utf8&w=&oq=dd&ri=1&sourceid=sugg&stj=1%3B0%3B0%3B0&stj2=0&stj0=1&stj1=0&hp=0&hp1=&sut=1909&sst0=1543542920653&lkt=3%2C1543542918642%2C1543542920549'

# 移除get参数的url
url2 = 'https://zhihu.sogou.com/zhihu'

# get 参数 = 'query=%E9%92%89%E9%92%89&ie=utf8&w=&oq=dd&ri=1&sourceid=sugg&stj=1%3B0%3B0%3B0&stj2=0&stj0=1&stj1=0&hp=0&hp1=&sut=1909&sst0=1543542920653&lkt=3%2C1543542918642%2C1543542920549'
params = {
    query: 钉钉
    ie: utf8
    w: 
    oq: dd
    ri: 1
    sourceid: sugg
    stj: 1;0;0;0
    stj2: 0
    stj0: 1
    stj1: 0
    hp: 0
    hp1: 
    sut: 1909
    sst0: 1543542920653
    lkt: 3,1543542918642,1543542920549
}
# 移除无用参数
params = {
    'query': 搜索词,
    'page': 页码,
    'ie': 'utf-8'
}
```

> 代码实现

```
# 前三页页面数据（1，2，3）
import requests
import os

# 创建一个文件夹
if not os.path.exists('./pages'):
    os.mkdir('./pages')

word = input('enter a word:')

# 动态指定页码的范围
start_pageNum = int(input('enter a start pageNum:'))
end_pageNum = int(input('enter a end pageNum:'))
# 自定义请求头信息
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}
# 1.指定url:设计成一个具有通用的url

# 请求url
url = 'https://zhihu.sogou.com/zhihu'
for page in range(start_pageNum, end_pageNum + 1):
    param = {
        'query': word,
        'page': page,
        'ie': 'utf-8'
    }
    response = requests.get(url=url, params=param, headers=headers)

    # 获取响应中的页面数据（指定页码（page））
    page_text = response.text

    # 进行持久化存储
    fileName = word + str(page) + '.html'
    filePath = 'pages/' + fileName
    with open(filePath, 'w', encoding='utf-8') as fp:
        fp.write(page_text)
        print('第%d页数据写入成功' % page)
        
```
