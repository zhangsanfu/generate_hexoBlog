---
title: Py007-01-02内置urllib模块
date: 2018-11-29 22:58:37
tags: M07
---

> urllib模块非爬虫的重点，核心为后面的request以及各种框架

### 内置urllib

概念：urllib是Python自带的一个用于爬虫的库，其主要作用就是可以通过代码模拟浏览器发送请求。其常被用到的子模块在Python3中的为urllib.request和urllib.parse，在Python2中是urllib和urllib2。

- 作用：可以使用代码模拟浏览器发起请求。request  parse

> 使用流程：

1. 指定url
2. 发请求
3. 获取页面数据
4. 持久化存储

#### 需求1爬取搜狗首页的页面数据

```
import urllib.request

#1.指定url
url = 'https://www.sogou.com/'

#2.发起请求:urlopen可以根据指定的url发起请求，切返回一个响应对象
response = urllib.request.urlopen(url=url)

#3.获取页面数据:read函数返回的就是响应对象中存储的页面数据(byte)
page_text = response.read()

#4.持久化存储
with open('./sougou.html','wb') as fp:
    # fp.write(page_text)
    # print(page_text) # 字节码
    print(page_text.decode()) # 转化为字符串
    fp.write(page_text.decode())#使用decode将page_text转成字符串类型
    print('写入数据成功')
```

#### 需求2搜狗搜关键字

> 注意：

- urllib模块的get参数如果有中文要自行转义，否则报错UnicodeEncodeError
- 使用urllib.parse.quote("中文参数") 转义中文

```
# 需求：爬取指定词条所对应的页面数据

import urllib.request
import urllib.parse

#指定url
url = 'https://www.sogou.com/web?query='
#url特性：url不可以存在非ASCII编码的字符数据
word = urllib.parse.quote("人民币")
url += word #有效的url

#发请求
response = urllib.request.urlopen(url=url)

#获取页面数据
page_text = response.read()


with open('renminbi.html','wb') as fp:
    fp.write(page_text)
    
'''
如果
word='人民币'
不经过转义则直接报错
UnicodeEncodeError: 'ascii' codec can't encode characters in position 15-17: ordinal not in range(128)
'''
```

是不是很麻烦如果多个参数就要多次处理？

#### 使用UA来伪装

- 反爬机制：网站检查请求的UA，如果发现UA是爬虫程序，则拒绝提供网站数据。
- User-Agent(UA)：请求载体的身份标识.
- 反反爬机制：伪装爬虫程序请求的UA

> 爬去百度首页

- 如果不用UA伪装，爬去的页面是不对的(百度已经做了反爬处理)
- 获取UA打开浏览器

```
1.开发者工具
2.检查
3.Network
4.访问百度
5.找到Request Headers的请求信息里的User-Agent字符串
形如
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36
```

```
import urllib.request

url = 'https://www.baidu.com/'

#UA伪装
#1.自定制一个请求对象
headers = {
    #存储任意的请求头信息
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.67 Safari/537.36'
    
}
#该请求对象的UA进行了成功的伪装
request = urllib.request.Request(url=url,headers=headers)

#2.针对自定制的请求对象发起请求
response = urllib.request.urlopen(request)

print(response.read())
```

#### 使用urllib发起post请求

> 需求：爬取百度翻译的翻译结果

```
import urllib.request
import urllib.parse

#1.指定url
url = 'https://fanyi.baidu.com/sug'

#post请求携带的参数进行处理  流程：
#1.将post请求参数封装到字典
data = {
    'kw':'西瓜'
}
#2.使用parse模块中的urlencode(返回值类型为str)进行编码处理
data = urllib.parse.urlencode(data)
#3.将步骤2的编码结果转换成byte类型
data = data.encode()

#2.发起post请求:urlopen函数的data参数表示的就是经过处理之后的post请求携带的参数
response = urllib.request.urlopen(url=url,data=data)

response.read()
res = response.read()
print(res)
```


