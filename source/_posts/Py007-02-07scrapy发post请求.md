---
title: Py007-02-07scrapy发post请求
date: 2018-12-02 15:05:43
tags: M07
---

### scrapy里post请求

```
scrapy startproject postPro

cd postPro

scrapy genspider postDemo www.baidu.com
```

#### start_requests方法

```
class PostdemoSpider(scrapy.Spider):
    name = 'postDemo'
    # allowed_domains = ['www.baidu.com']
    start_urls = ['https://fanyi.baidu.com/sug']

    # start_requests是父类的方法：对start_urls里的url进行get请求发送
    '''
    # 大概实现如下
    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.Request(url=url,callback=self.parse)
    '''
    
    def parse(self, response):
        pass
```

#### 发送post请求

start_requests 默认发送get 如何发送post呢？

> 重写 start_requests 方法的实现

```
# 发起post
    '''
    1.将Request方法的method赋值成post(不推荐)
    2.FormRequest() 这个方法也可以发起post请求(推荐)
    '''
    def start_requests(self):
        # post请求参数
        data = {
            'kw':'dog'
        }
        for url in self.start_urls:
            # formdata :请求参数对应的字典
            yield scrapy.FormRequest(url=url,formdata=data,callback=self.parse)
```

### 需求：百度翻译中指定词条的翻译结果 进行获取

```
# -*- coding: utf-8 -*-
import scrapy

class PostdemoSpider(scrapy.Spider):
    name = 'postDemo'
    # allowed_domains = ['www.baidu.com']
    start_urls = ['https://fanyi.baidu.com/sug']

    # start_requests是父类的方法：对start_urls里的url进行get请求发送
    '''
    # 大概实现如下
    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.Request(url=url,callback=self.parse)
    '''

    # 发起post
    '''
    1.将Request方法的method赋值成post
    2.FormRequest() 这个方法也可以发起post请求(推荐)
    '''
    def start_requests(self):
        # post请求参数
        data = {
            'kw':'dog'
        }
        for url in self.start_urls:
            # formdata :请求参数对应的字典
            yield scrapy.FormRequest(url=url,formdata=data,callback=self.parse)

    def parse(self, response):
        print(response.text)
        pass

```

> 注意：别忘了修改settings.py里的设置

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议
```

> 验证结果

```
scrapy crawl postDemo --nolog
```

### 总结

> 如何发post方法？

一定要对start_requests方法进行重写

- 1.将Request方法的method赋值成post
- 2.FormRequest() 这个方法也可以发起post请求(推荐)

