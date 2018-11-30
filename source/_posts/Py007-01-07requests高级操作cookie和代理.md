---
title: Py007-01-07requests高级操作cookie和代理
date: 2018-11-30 21:27:14
tags: M07
---

### requests模块高级：

#### cookie：

> 基于用户的用户数据
- 需求：爬取张三用户的豆瓣网的个人主页页面数据

- 在没有登录的情况下浏览器访问 https://www.douban.com/people/185687620/
- 发现不是对应主页信息而是要求你登录的页面

```
#问题：没有获取个人主页的页面数据
#原因：爬虫程序没有严格遵从浏览器的请求流程
import requests

#1.指定url
url = 'https://www.douban.com/people/185687620/'
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
    }
#2.发起请求
response = requests.get(url=url,headers=headers)

#3.获取页面数据
page_text = response.text

#4.持久化存储
with open('./douban.html','w',encoding='utf-8') as fp:
    fp.write(page_text)
```

> #### 无法访问对应页码的成因

```
结果发现，写入到文件中的数据，不是张三个人页面的数据，而是豆瓣登陆的首页面，why？首先我们来回顾下cookie的相关概念及作用：
```

- cookie概念：当用户通过浏览器首次访问一个域名时，访问的web服务器会给客户端发送数据，以保持web服务器与客户端之间的状态保持，这些数据就是cookie。

- cookie作用：我们在浏览器中，经常涉及到数据的交换，比如你登录邮箱，登录一个页面。我们经常会在此时设置30天内记住我，或者自动登录选项。那么它们是怎么记录信息的呢，答案就是今天的主角cookie了，Cookie是由HTTP服务器设置的，保存在浏览器中，但HTTP协议是一种无状态协议，在数据交换完毕后，服务器端和客户端的链接就会关闭，每次交换数据都需要建立新的链接。就像我们去超市买东西，没有积分卡的情况下，我们买完东西之后，超市没有我们的任何消费信息，但我们办了积分卡之后，超市就有了我们的消费信息。cookie就像是积分卡，可以保存积分，商品就是我们的信息，超市的系统就像服务器后台，http协议就是交易的过程。

- 经过cookie的相关介绍，其实你已经知道了为什么上述案例中爬取到的不是张三个人信息页，而是登录页面。那应该如何抓取到张三的个人信息页呢？

> 思路：

1. 我们需要使用爬虫程序对豆瓣的登录时的请求进行一次抓取，获取请求中的cookie数据
2. 在使用个人信息页的url进行请求时，该请求需要携带 1 中的cookie，只有携带了cookie后，服务器才可识别这次请求的用户信息，方可响应回指定的用户信息页数据


#### cookie作用：服务器端使用cookie来记录客户端的状态信息。

> 实现流程：

1. 执行登录操作（获取cookie）
2. 在发起个人主页请求时，需要将cookie携带到该请求中

注意：session对象：发送请求（会将cookie对象进行自动存储）

```
import requests

session = requests.session()
#1.发起登录请求：将cookie获取，切存储到session对象中
login_url = 'https://accounts.douban.com/login'
data = {
    "source": "None",
    "redir": "https://www.douban.com/people/185687620/",
    "form_email": "15027900535",
    "form_password": "bobo@15027900535",
    "login": "登录",
}
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
}
#使用session发起post请求
login_response = session.post(url=login_url,data=data,headers=headers)

#2.对个人主页发起请求（session（cookie）），获取响应页面数据
url = 'https://www.douban.com/people/185687620/'
response = session.get(url=url,headers=headers)
page_text = response.text

with open('./douban110.html','w',encoding='utf-8') as fp:
    fp.write(page_text)
```
    
#### 代理

> 基于requests模块的代理操作

- 什么是代理

代理就是第三方代替本体处理相关事务。例如：生活中的代理：代购，中介，微商......

- 爬虫中为什么需要使用代理

一些网站会有相应的反爬虫措施，例如很多网站会检测某一段时间某个IP的访问次数，如果访问频率太快以至于看起来不像正常访客，它可能就会会禁止这个IP的访问。所以我们需要设置一些代理IP，每隔一段时间换一个代理IP，就算IP被禁止，依然可以换个IP继续爬取。

- 代理的分类：

正向代理：代理客户端获取数据。正向代理是为了保护客户端防止被追究责任。

反向代理：代理服务器提供数据。反向代理是为了保护服务器或负责负载均衡。

- 免费代理ip提供网站

1. http://www.goubanjia.com/ (推荐)

2. 西祠代理

3. 快代理

> 代理实例

```
import requests

url = 'https://www.baidu.com/s?ie=utf-8&wd=ip'

headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36',
}

#将代理ip封装到字典
proxy = {
    'http':'77.73.69.120:3128',
   # 'https':'xxxx' 也可以是https
}
#更换网路IP
response = requests.get(url=url,proxies=proxy,headers=headers)

with open('./daili.html','w',encoding='utf-8') as fp:
    fp.write(response.text)
```

> 注意点

- 请求的协议要与代理ip的协议统一
