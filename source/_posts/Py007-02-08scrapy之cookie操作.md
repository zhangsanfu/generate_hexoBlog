---
title: Py007-02-08scrapy之cookie操作
date: 2018-12-02 16:36:45
tags: M07
---

### cookie操作

> 豆瓣登陆指定页面

```
scrapy startproject doubanPro

cd doubanPro

scrapy genspider douban www.douban.com
```

> #### 第一步 先该settins.py里的一些配置

```
USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36'

ROBOTSTXT_OBEY = False
```

> #### 第二步 

- 修改起始url 也就是登陆的url
- 重写 start_requests方法  发起post请求 (这里有登录操作，携带post参数)
- 登录成功后 通过yield 来发送第二个请求 也就是个人主页页面数据
- 自定义 请求成功后的parse方法 来进行个人页面数据的解析操作

```
# -*- coding: utf-8 -*-
import scrapy


class DoubanSpider(scrapy.Spider):
    name = 'douban'
    allowed_domains = ['www.douban.com']
    start_urls = ['http://www.douban.com/accounts/login']

    # 重写 start_requests
    def start_requests(self):
        # 将请求参数封装到 字典中
        # 豆瓣登陆页面 https://accounts.douban.com/login
        data = {
            'source': 'index_nav',
            'form_email': '15027900535',
            'form_password': 'bobo@15027900535',
        }
        for url in self.start_urls:
            yield scrapy.FormRequest(url=url,formdata=data,callback=self.parse)
    def parse(self, response):

        # 登陆成功后的页面数据进行存储

        fp = open('main.html','w',encoding='utf-8')
        fp.write(response.text)

        # 获取当前用户个人主页对应的页面数据
        url = 'https://www.douban.com/people/185687620/'

        yield scrapy.Request(url=url,callback=self.parseSecondPage)

    # 定义跳转指定页面的 parse方法
    def parseSecondPage(self,response):
        fp = open('second.html', 'w', encoding='utf-8')
        fp.write(response.text)

        # 可以对当前用户的个人主页的页面数据进行解析操作
```

> #### 第三步 执行爬虫命令

```
scrapy crawl douban --nolog
```


### 总结

> 通过scrapy进行cookie操作时，并不需要将cookie进行特定的提取，也不需要将cookie加载到我们的请求对象中去

- 涉及登录的时候，如果服务器给我们设置了cookie,scrapy在第二次请求页面的时候自动帮我们携带cookie 
