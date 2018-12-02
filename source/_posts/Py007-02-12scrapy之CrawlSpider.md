---
title: Py007-02-12scrapy之CrawlSpider
date: 2018-12-02 20:36:00
tags: M07
---

> #### 如果想对网址进行全站的爬取？

```
解决方案：
1. 手动请求的发送 (yield) 递归调用

2. CrawlSpider(推荐)
```

### CrawlSpider

- 其实就是Spider的一个子类。
- 功能更加强大(链接提取器，规则解析器)



### 项目实战

> 项目初始化

```
scrapy startproject crawlSpiderPro

cd crawlSpiderPro

# 以前生成应用是 scrapy genspider chouti dig.chouti.com

# 此时我们的项目是 CrawlSpider的  所以 生成命令要修改一下
# 此时我们的项目是 CrawlSpider的  所以 生成命令要修改一下
# 此时我们的项目是 CrawlSpider的  所以 生成命令要修改一下

scrapy genspider -t crawl  chouti dig.chouti.com
```

> 创建基于CrawlSpider的的爬虫文件

```
scrapy genspider -t 爬虫名称 起始url
```

> 此时 spiders/chouti.py 内容如下

```
# -*- coding: utf-8 -*-
import scrapy
# 链接提取器
from scrapy.linkextractors import LinkExtractor
# 规则解析器
from scrapy.spiders import CrawlSpider, Rule

class ChoutiSpider(CrawlSpider):
    name = 'chouti'
    # allowed_domains = ['dig.chouti.com']
    start_urls = ['https://dig.chouti.com/']
    
    # 实例化一个链接提取器
    # 链接提取器：用来提取指定的链接 (url)
    # allow参数：赋值一个正则表达式
    # 链接提取器就可以根据正则在页面中提取指定的链接
    # 提取到的链接会全部交给规则解析器
    link = LinkExtractor(allow=r'Items/')
    rules = (
        # 实例化一个规则解析器
        # 规则解析器接收了 链接提取器发送的链接后，就会对这些链接发起请求，获取链接对应的页面内容，就会根据指定的规则对页面内容中指定的数据值进行解析
        # callback:指定一个解析规则(方法/函数)
        Rule(link, callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        pass
```

> settings.py修改

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议
```

#### 经过如上初始化 我们来对抽屉新热榜进行 爬取

```
访问 https://dig.chouti.com/

看到底部的分页器中的 a标签的href="/all/hot/recent/12"

这就是对应的页面url路径
```

> #### 修改代码里的 rule

```
# -*- coding: utf-8 -*-
import scrapy
# 链接提取器
from scrapy.linkextractors import LinkExtractor
# 规则解析器
from scrapy.spiders import CrawlSpider, Rule


class ChoutiSpider(CrawlSpider):
    name = 'chouti'
    # allowed_domains = ['dig.chouti.com']
    start_urls = ['https://dig.chouti.com/']

    # 实例化一个链接提取器
    # 链接提取器：用来提取指定的链接 (url)
    # allow参数：赋值一个正则表达式
    # 链接提取器就可以根据正则在页面中提取指定的链接
    # 提取到的链接会全部交给规则解析器
    link = LinkExtractor(allow=r'/all/hot/recent/\d+')
    rules = (
        # 实例化一个规则解析器
        # 规则解析器接收了 链接提取器发送的链接后，就会对这些链接发起请求，获取链接对应的页面内容，就会根据指定的规则对页面内容中指定的数据值进行解析
        # callback:指定一个解析规则(方法/函数)
        # follow: 先设置为False 后期详细说明
        Rule(link, callback='parse_item', follow=False),
    )

    def parse_item(self, response):
        print(response)
```

> 执行命令

```
scrapy crawl chouti --nolog

# 打印如下信息
<200 https://dig.chouti.com/all/hot/recent/1>
<200 https://dig.chouti.com/all/hot/recent/5>
<200 https://dig.chouti.com/all/hot/recent/4>
<200 https://dig.chouti.com/all/hot/recent/7>
<200 https://dig.chouti.com/all/hot/recent/9>
<200 https://dig.chouti.com/all/hot/recent/3>
<200 https://dig.chouti.com/all/hot/recent/6>
<200 https://dig.chouti.com/all/hot/recent/10>
<200 https://dig.chouti.com/all/hot/recent/2>
<200 https://dig.chouti.com/all/hot/recent/8>
```

> #### follow参数

```
# follow代表 是否将提取器继续作用到 链接提取器取出的链接所表示的页面数据中

# 刚刚执行爬虫命令后
scrapy crawl chouti --nolog
# 仅仅打印了 10个页码的链接
# 而我们在 到第十页的时候 https://dig.chouti.com/all/hot/recent/10

# 底部的分页器是
1. 。。。 7 8 9 10 11 12 13 14 

# 这就意味着 当到第十页的时候  会继续提取后面的页面链接 ==> 直到最后一页数据
```

> #### 修改follow:True

```
# -*- coding: utf-8 -*-
import scrapy
# 链接提取器
from scrapy.linkextractors import LinkExtractor
# 规则解析器
from scrapy.spiders import CrawlSpider, Rule


class ChoutiSpider(CrawlSpider):
    name = 'chouti'
    # allowed_domains = ['dig.chouti.com']
    start_urls = ['https://dig.chouti.com/']

    # 实例化一个链接提取器
    # 链接提取器：用来提取指定的链接 (url)
    # allow参数：赋值一个正则表达式
    # 链接提取器就可以根据正则在页面中提取指定的链接
    # 提取到的链接会全部交给规则解析器
    link = LinkExtractor(allow=r'/all/hot/recent/\d+')
    rules = (
        # 实例化一个规则解析器
        # 规则解析器接收了 链接提取器发送的链接后，就会对这些链接发起请求，获取链接对应的页面内容，就会根据指定的规则对页面内容中指定的数据值进行解析
        # callback:指定一个解析规则(方法/函数)
        # follow: 先设置为False 后期详细说明
        # follow代表 是否将提取器继续作用到 链接提取器取出的链接所表示的页面数据中
        Rule(link, callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        print(response)
```

> 再次执行爬虫命令

```
scrapy crawl chouti --nolog

# 打印如下
<200 https://dig.chouti.com/all/hot/recent/1>
<200 https://dig.chouti.com/all/hot/recent/2>
<200 https://dig.chouti.com/all/hot/recent/3>
...
...
...
<200 https://dig.chouti.com/all/hot/recent/115>
<200 https://dig.chouti.com/all/hot/recent/116>
<200 https://dig.chouti.com/all/hot/recent/117>
<200 https://dig.chouti.com/all/hot/recent/118>
<200 https://dig.chouti.com/all/hot/recent/119>
<200 https://dig.chouti.com/all/hot/recent/120>
```