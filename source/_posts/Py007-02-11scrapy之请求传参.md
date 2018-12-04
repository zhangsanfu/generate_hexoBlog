---
title: Py007-02-11scrapy之请求传参
date: 2018-12-02 18:01:49
tags: M07
---

### 请求传参

爬取的数据值不在同一页面中

> 需求爬取  www.id97.com 电影数据和对应电影详情页面的数据

#### 项目初始化

```
scrapy startproject moviePro

cd moviePro/

scrapy genspider movie www.id97.com
```

> spiders/movie.py

```
import scrapy

class MovieSpider(scrapy.Spider):
    name = 'movie'
    # allowed_domains = ['www.id97.com']
    '''
    www.id97.com 的域名已经换了 www.55xia.com
    '''
    start_urls = ['http://www.id97.com/']

    def parse(self, response):
        # 电影名称，类型，导演，语言，片长
        # 浏览器里 找到电影列表外层容器 copy-xpath
        div_list = response.xpath('/html/body/div[1]/div[2]/div')
        for div in div_list:
            name = div.xpath('.//div[@class="meta"]/h1/a/text()').extract_first()
            kind = div.xpath('.//div[@class="otherinfo"]//text()').extract_first()
            url = div.xpath('.//div[@class="meta"]/h1/a/@href').extract_first()

            # 而此时 导演和演员  不再当前页面 在对应的详情页面
            # 需要对url发请求，获取页面数据，进行数据解析

            yield scrapy.Request(url=url,callback=self.secondParse)


    # 专门用于处理二级自页面的解析函数
    def secondParse(self,response):
        author = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[1]/td[2]/a/text()').extract_first()
        language = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[6]/td[2]/text()').extract_first()
        longTime = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[8]/td[2]/text()').extract_first()


```

> items.py

```
import scrapy

class MovieproItem(scrapy.Item):
    name = scrapy.Field()
    kind = scrapy.Field()
    actor = scrapy.Field()
    language = scrapy.Field()
    longTime = scrapy.Field()
```

> #### 问题来了，数据分散在两次请求里，如何处理数据？

> 请求传参

```
# -*- coding: utf-8 -*-
import scrapy

from moviePro.items import MovieproItem
class MovieSpider(scrapy.Spider):
    name = 'movie'
    # allowed_domains = ['www.id97.com']
    '''
    www.id97.com 的域名已经换了 www.55xia.com
    '''
    start_urls = ['http://www.id97.com/']

    def parse(self, response):
        # 电影名称，类型，导演，语言，片长
        # 浏览器里 找到电影列表外层容器 copy-xpath
        div_list = response.xpath('/html/body/div[1]/div[2]/div')
        for div in div_list:
            name = div.xpath('.//div[@class="meta"]/h1/a/text()').extract_first()
            kind = div.xpath('.//div[@class="otherinfo"]//text()').extract_first()
            url = div.xpath('.//div[@class="meta"]/h1/a/@href').extract_first()

            # 创建items对象
            item = MovieproItem()
            item['name'] = name
            item['kind'] = kind
            # 如何将剩下的数据进行传递呢？  ====》 请求传参(meta参数)
            '''
            meta 需赋值为一个字典  将item封装到字典里
            '''

            # 而此时 导演和演员  不再当前页面 在对应的详情页面
            # 需要对url发请求，获取页面数据，进行数据解析

            yield scrapy.Request(url=url,callback=self.secondParse,meta={'item':item})

    # 专门用于处理二级自页面的解析函数
    def secondParse(self,response):
        author = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[1]/td[2]/a/text()').extract_first()
        language = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[6]/td[2]/text()').extract_first()
        longTime = response.xpath('/html/body/div[1]/div/div/div[1]/div[1]/div[2]/table/tbody/tr[8]/td[2]/text()').extract_first()

        # 取出Request方法的meta参数
        item = response.meta['item']
        item['author'] = author
        item['language'] = language
        item['longTime'] = longTime

        # 将item提交给管道
        yield item
```

> 编写pipelines.py

```
# -*- coding: utf-8 -*-
class MovieproPipeline(object):
    fp = None

    def open_spider(self,spider):
        self.fp = open('movie.txt','w',encoding='utf-8')

    def close_spider(self,spider):
        self.fp.close()

    def process_item(self, item, spider):
        detail = item['name']+':'+item['kind']+':'+item['author']+':'+item['language']+':'+item['longTime']

        self.fp.write(detail)
        return item
```

> 修改settings.py

```
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议

# 解开注释
ITEM_PIPELINES = {
   'moviePro.pipelines.MovieproPipeline': 300,
}
```

> 执行命令

```
scrapy crawl movie --nolog

此时发现movie.txt 是空的  因为起始url是首页 并不是对应的电影列表页面
实际url为
https://www.55xia.com/movie/

再次运行
```