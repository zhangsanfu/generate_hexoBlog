---
title: Py007-02-05scrapy多个url爬取
date: 2018-12-02 13:51:40
tags: M07
---

### 爬取多个页面url数据

```
# 糗事百科  文字标签页面的数据   它有13页
https://www.qiushibaike.com/text/
```

#### 项目实战

```
# 切换到桌面
cd ~/Desktop 


# 创建项目
scarpy startproject qiubaiByPages


# 创建成功后进入该目录
cd qiubaiByPages/

# 创建spider 爬虫应用
scrapy genspider qiubai www.qiushibaike.com/text
```

> #### 第一步 修改spiders/qiubai.py

```
import scrapy

class QiubaiSpider(scrapy.Spider):
    name = 'qiubai'
    allowed_domains = ['www.qiushibaike.com/text']
    start_urls = ['https://www.qiushibaike.com/text/']

    def parse(self, response):
        div_list = response.xpath('//*[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div[@class="author clearfix"]/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # 创建items对象，将解析内容存储到items对象里

        pass

```

> #### 第二步 创建items对象  items.py

```
import scrapy

class QiubaibypagesItem(scrapy.Item):
    author = scrapy.Field()
    content = scrapy.Field()
```

> #### 第三步 在spiders/qiubai.py里导入items,并把item数据提交给管道文件

```
import scrapy
from qiubaiByPages.items import QiubaibypagesItem

class QiubaiSpider(scrapy.Spider):
    name = 'qiubai'
    # 建议注释掉 allowed_domains 图片的资源域名可能与此不一致
    # allowed_domains = ['www.qiushibaike.com/text']
    start_urls = ['https://www.qiushibaike.com/text/']

    def parse(self, response):
        div_list = response.xpath('//*[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div[@class="author clearfix"]/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # 创建items对象，将解析内容存储到items对象里
            item = QiubaibypagesItem()
            item['author'] = author
            item['content'] = content

            # 将item对象提交给管道文件
            yield item
```

> 修改管道文件pipelines.py

```
# -*- coding: utf-8 -*-

class QiubaibypagesPipeline(object):
    fp = None
    def open_spider(self,spider):
        print('开始爬虫')
        self.fp = open('./qiubai.txt','w',encoding='utf-8')

    def close_spider(self,spider):
        print('结束爬虫')
        self.fp.close()

    def process_item(self, item, spider):
        self.fp.write(item['author']+':'+item['content']+'\n\n\n')
        return item
```

> #### 第四步 修改配置文件settings.py

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议


# 解开注释 管道操作配置
ITEM_PIPELINES = {
   'qiubaiByPages.pipelines.QiubaibypagesPipeline': 300,
}
```

> #### 最后 回到刚刚的命令行 执行爬虫命令

把基础的流程跑通

```
scrapy crawl qiubai --nolog
```


### 第二部分

- 上述流程跑通后，仅仅是一页的数据而
- 我们的需求是它的所有分页数据

```
# 该url的分页数据
https://www.qiushibaike.com/text/
```

#### 解决方案

- 请求的手动发送

> 分析spiders/qiubai.py

```
def parse(self, response):
    ...
    # 将item对象提交给管道文件
    yield item
'''
当yield item都执行完毕  意味着：

https://www.qiushibaike.com/text/
这一页面的数据爬取完毕
'''
```

> 手动处理下一次请求(仅仅以第二页数据为例)理解版本的parse函数修改

```
    def parse(self, response):
        div_list = response.xpath('//*[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div[@class="author clearfix"]/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # 创建items对象，将解析内容存储到items对象里
            item = QiubaibypagesItem()
            item['author'] = author
            item['content'] = content

            # 将item对象提交给管道文件
            yield item

        # 请求的手动发送
        url = 'https://www.qiushibaike.com/text/page/2'
        '''
        url代表下一次请求的 url
        callback代表将请求到的页面数据进行解析  而这个解析函数正是我们这里的parse函数
        '''
        yield scrapy.Request(url=url,callback=self.parse)
```

> ##### 注意 这个版本 手动发送请求属于递归调用 由于没有结束条件所以会一直递归下去。。。。。

> ##### 注意 这个版本 手动发送请求属于递归调用 由于没有结束条件所以会一直递归下去。。。。。

> ##### 注意 这个版本 手动发送请求属于递归调用 由于没有结束条件所以会一直递归下去。。。。。

> #### 完整版的qiubai.py文件

```
import scrapy
from qiubaiByPages.items import QiubaibypagesItem

class QiubaiSpider(scrapy.Spider):
    name = 'qiubai'
    # 注视掉这个 allowed_domains
    # allowed_domains = ['www.qiushibaike.com/text']
    start_urls = ['https://www.qiushibaike.com/text/']

    # 设计通用的url模版
    url = 'https://www.qiushibaike.com/text/page/%d'
    pageNum = 1

    def parse(self, response):
        div_list = response.xpath('//*[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div[@class="author clearfix"]/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # 创建items对象，将解析内容存储到items对象里
            item = QiubaibypagesItem()
            item['author'] = author
            item['content'] = content

            # 将item对象提交给管道文件
            yield item

        # 请求的手动发送
        if self.pageNum < 13: # 13表示是最后一页的页码
            print('爬取到了第%d的页面数据'%self.pageNum)
            self.pageNum += 1 # 页码增加
            new_url = format(self.url%self.pageNum)
            yield scrapy.Request(url=new_url,callback=self.parse)

```
