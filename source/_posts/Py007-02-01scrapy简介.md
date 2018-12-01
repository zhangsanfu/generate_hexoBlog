---
title: Py007-02-01scrapy简介
date: 2018-12-01 12:33:28
tags: M07
---

### scrapy简介

- 环境安装
- 基础使用

#### 一.什么是Scrapy？

> Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架，非常出名，非常强悍。所谓的框架就是一个已经被集成了各种功能（高性能异步下载，队列，分布式，解析，持久化等）的具有很强通用性的项目模板。对于框架的学习，重点是要学习其框架的特性、各个功能的用法即可。

#### 二.安装

```
　　Linux：

      pip3 install scrapy

 

　　Windows：

      a. pip3 install wheel

      b. 下载twisted http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted

      c. 进入下载目录，执行 pip3 install Twisted‑17.1.0‑cp35‑cp35m‑win_amd64.whl

      d. pip3 install pywin32

      e. pip3 install scrapy
```

#### 三.基础使用

> 1.创建项目：scrapy startproject 项目名称

```
项目结构：

project_name/
   scrapy.cfg：
   project_name/
       __init__.py
       items.py
       pipelines.py
       settings.py
       spiders/
           __init__.py

scrapy.cfg   项目的主配置信息。（真正爬虫相关的配置信息在settings.py文件中）
items.py     设置数据存储模板，用于结构化数据，如：Django的Model
pipelines    数据持久化处理
settings.py  配置文件，如：递归的层数、并发数，延迟下载等
spiders      爬虫目录，如：创建文件，编写爬虫解析规则
```

>　2.创建爬虫应用程序：

```
cd project_name（进入项目目录）

scrapy genspider 应用名称 爬取网页的起始url （例如：scrapy genspider qiubai www.qiushibaike.com）
```

> 3.编写爬虫文件:在步骤2执行完毕后，会在项目的spiders中生成一个应用名的py爬虫文件，文件源码如下：

```
# -*- coding: utf-8 -*-
import scrapy

class QiubaiSpider(scrapy.Spider):
    #爬虫文件名称  ：通过这个name可以指定的定位到某一个具体的爬虫文件
    name = 'qiubai' 
    #允许的域名（爬取指定域名下的页面数据）
    allowed_domains = ['https://www.qiushibaike.com/']
    #起始url： 列表的形式，当前工程将要爬取页面对应的url
    start_urls = ['https://www.qiushibaike.com/']

    '''
    # 不能对百度进行爬取  因为allowed_domains的域名 跟百度不是一个域名
    start_urls = ['https://www.qiushibaike.com/','www.baidu.com'] 
    '''

     # （回调）解析方法：对获取的页面数据进行指定内容的解析
     # response：根据起始url列表发请求，请求成功后返回的响应对象
     # 该函数返回值必须为可迭代对象或者NUll
     def parse(self, response):
        print(response.text) #获取字符串类型的响应内容
        print(response.body)#获取字节类型的相应内容
```

> 4.设置修改settings.py配置文件相关配置:

修改内容及其结果如下：(初学阶段仅仅做如下设置)

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议
```

> 5.执行爬虫程序：

```
scrapy crawl  应用名称
如
scrapy crawl qiubai
# 会显示一堆 日志信息
```

> #### 忽略日志信息

```
scrapy crawl 应用名称 --nolog
```

#### 四.小试牛刀：将糗百首页中文字标签对应的内容和标题进行爬取

- https://www.qiushibaike.com/text/

> #### 1. 创建工程

```
scrapy startproject qiubaiPro
```

> #### 2. 创建一个爬虫程序

```
# 进入到工程目录
cd qiubaiPro
scrapy genspider qiubai www.qiushibaike.com/text
```

> #### 3. 编写代码

- 注意默认的协议是http 所以需要手动改对应的网址协议

```
# 默认
start_urls = ['http://www.qiushibaike.com/']
# 更改后
start_urls = ['https://www.qiushibaike.com/']
```

- 建议注释掉 allowed_domains

```
# 因为爬取内容的图片不一定是本域名下的  如果设置了  可能获取不到
allowed_domains = ['https://www.qiushibaike.com/']
```

- 解析方式

```
推荐  xpath (scrapy里集成了  xpath解析接口)
```

- 修改配置

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议
```

```
import scrapy

class QiubaiSpider(scrapy.Spider):
    name = 'qiubai'
    # 最好注释这个允许域名   因为图片如果是其他域名的就获取不到了
    # allowed_domains = ['www.qiushibaike.com/text']

    # 默认生成的协议是http的需要手动修改为 https
    start_urls = ['https://www.qiushibaike.com/text/']

    def parse(self, response):
        # 推荐xpath(scrapy里集成了xpath解析接口)

        # 每个段子的外层容器
        div_list = response.xpath('//div[@id="content-left"]/div')

        for div in div_list:
            # xpath解析指定内容被存到一个Selector对象里
            '''
            author = div.xpath('./div/a[2]/h2/text()')
            content = div.xpath('.//div[@class="content"]/span/text()')

            print(author) # [<Selector xpath='./div/a[2]/h2/text()' data='\n好吃的焦糖饼干～\n'>]
            # xpath返回的 不是直接的内容而是一个列表  列表里是一个selector对象
            '''

            # extract()方法  可以将selector里存储的数据值拿到
            # author = div.xpath('./div/a[2]/h2/text()').extract()
            # content = div.xpath('.//div[@class="content"]/span/text()').extract()
            # print(author) # ['\n好吃的焦糖饼干～\n']
            author = div.xpath('./div/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()
            print(author) #  \n好吃的焦糖饼干～\n

        pass

```

> 执行命令

```
scrapy crawl 爬虫名称 ：该种执行形式会显示执行的日志信息
scrapy crawl 爬虫名称 --nolog：该种执行形式不会显示执行的日志信息
```