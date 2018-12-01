---
title: Py007-02-02scrapy持久化存储
date: 2018-12-01 15:11:54
tags: M07
---

### 持久化存储操作

两种方式

- a 磁盘文件
- b 数据库

> #### 磁盘文件方式

- 基于终端指令的方式
- 基于管道的方式

#### 终端指令的方式

- 保证爬虫文件的parse方法中有可迭代类型对象（通常为列表or字典）的返回，该返回值
- 通过终端指令的形式写入指定格式的文件中进行持久化操作。

> #### 基于上一篇内容来处理

qiubai.py

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

        # step001 数据容器（可迭代对象）
        data_list = []
        for div in div_list:
            author = div.xpath('./div/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            obj = {
                'author':author,
                'content':content
            }
            data_list.append(obj)

        # step002 返回可迭代对象
        return data_list
```

> 通过命令来存储数据

执行输出指定格式进行存储：将爬取到的数据写入不同格式的文件中进行存储

```
'''
scrapy crawl 爬虫名称 -o xxx.json
scrapy crawl 爬虫名称 -o xxx.xml
scrapy crawl 爬虫名称 -o xxx.csv
'''

scrapy crawl qiubai -o qiubai.csv --nolog

scrapy crawl qiubai -o qiubai.txt --nolog

scrapy crawl qiubai -o qiubai.json --nolog
```

#### 基于管道

- 1.items 存储解析到的页面数据 类似django里的models
- 2.pipelines 处理持久化存储的相关操作
- 3.代码实现流程
    ```
    1. 将解析的数据存到 items对象
    2. 使用yield 关键字将 items提交给管道文件进行处理
    3. 在管道文件中编写代码完成数据存储
    4. 在配置文件里开启'管道'操作
    ```

> ### 继续操作刚刚的爬虫文件

#### 第一步 将解析的数据存到 items对象

> ##### step001 qiubai.py

```
# 引入items模块
from qiubaiPro.qiubaiPro.items import QiubaiproItem
```

> ##### step002 声明items对应的字段  如 author和 content

items.py

```
import scrapy

class QiubaiproItem(scrapy.Item):
    # 声明字段
    author = scrapy.Field()
    content = scrapy.Field()
```

> ##### step003添加 数据到items

```
import scrapy
from qiubaiPro.items import QiubaiproItem

class QiubaiSpider(scrapy.Spider):
    name = 'qiubai'

    start_urls = ['https://www.qiushibaike.com/text/']

    def parse(self, response):
        # 推荐xpath(scrapy里集成了xpath解析接口)

        # 每个段子的外层容器
        div_list = response.xpath('//div[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # step001 将解析到的数据 存到items对象
            item = QiubaiproItem()
            item['author'] = author
            item['content'] = content
```

#### 第二步 ：使用yield 关键字将 items提交给管道文件进行处理

```
# 将解析到的数据 存到items对象
item = QiubaiproItem()
item['author'] = author
item['content'] = content

# step002 将item对象提交给管道
yield item

# 去编写对象的 pipelines.py
```

#### 第三步 ：在管道文件中编写代码完成数据存储

pipelines.py

```
class QiubaiproPipeline(object):
    # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
    '''
    :param
    # 参数item:表示接收到的item对象
    '''
    # 每向管道文件提交一次item，则该方法就会执行一次
    def process_item(self, item, spider):
        # 取出item对应的数据值
        author = item['author']
        content = item['content']

        # 持久化存储
        with open('./qiubai_pipe.txt','w',encoding='utf-8') as fp:
            fp.write(author+':'+content+'\n\n\n')
        return item
```

#### 第四步 在配置文件里开启'管道'操作

settings.py

```
# 搜索pipeline

# 解开注释
ITEM_PIPELINES = {
   'qiubaiPro.pipelines.QiubaiproPipeline': 300,
}

# 300代表优先级
```

#### 执行爬虫命令

```
scrapy crawl qiubai 

# 此时发现qiubai_pipe.txt里只有一条数据

# 原因是
'''
qiubai.py里的 yield item 写在for循环里

    def parse(self, response):
        # 推荐xpath(scrapy里集成了xpath解析接口)

        # 每个段子的外层容器
        div_list = response.xpath('//div[@id="content-left"]/div')

        for div in div_list:
            author = div.xpath('./div/a[2]/h2/text()').extract_first()
            content = div.xpath('.//div[@class="content"]/span/text()').extract_first()

            # step001 将解析到的数据 存到items对象
            item = QiubaiproItem()
            item['author'] = author
            item['content'] = content

            # step002 将item对象提交给管道
            yield item
------------------------------------------
每向管道文件提交一次item，则该方法就会执行一次
pipelines.py里的

def process_item(self, item, spider):
    # 取出item对应的数据值
    author = item['author']
    content = item['content']

    # 持久化存储
    with open('./qiubai_pipe.txt','w',encoding='utf-8') as fp:
        fp.write(author+':'+content+'\n\n\n')
    return item
所以只存了 最后一次的爬虫数据
'''
```

#### 解决存储bug

pipelines.py

```
class QiubaiproPipeline(object):
    fp = None
    # 在爬虫过程中，该方法只会在开始爬虫的时候调用一次
    def open_spider(self,spider):
        print('开始爬虫')
        self.fp = open('./qiubai_pipe.txt', 'w', encoding='utf-8')

    # 在爬虫过程中，该方法只会在爬虫结束的时候调用一次
    def close_spider(self, spider):
        print('结束爬虫')
        self.fp.close()

    # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
    '''
    :param
    # 参数item:表示接收到的item对象
    '''
    def process_item(self, item, spider):
        # 取出item对应的数据值
        author = item['author']
        content = item['content']

        # 持久化存储
        self.fp.write(author+':'+content+'\n\n\n')
        return item
```

> #### 再次执行爬虫命令

```
scrapy crawl qiubai --nolog
```

#### 基于mysql持久化存储

> 与管道方式大体相同,唯一不同的是第三步  此时不是存入文件而是 sql录入

```
1. 将解析的数据存到 items对象
2. 使用yield 关键字将 items提交给管道文件进行处理
3. 在管道文件中编写代码sql录入语句完成数据存储
4. 在配置文件里开启'管道'操作
```


```
import pymysql
class QiubaiproPipeline(object):
    conn = None
    # 在爬虫过程中，该方法只会在开始爬虫的时候调用一次
    def open_spider(self,spider):
        print('开始爬虫')
        # 连接数据库
        self.conn = pymysql.Connect(host='127.0.0.1',port=3306,user='root',password='831015',db='qiubai')


    # 在爬虫过程中，该方法只会在爬虫结束的时候调用一次
    def close_spider(self, spider):
        print('结束爬虫')
        self.cursor.close()
        self.conn.close()

    # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
    '''
    :param
    # 参数item:表示接收到的item对象
    '''
    def process_item(self, item, spider):
        # 取出item对应的数据值
        author = item['author']
        content = item['content']

        # 连接数据库
        # 执行sql
        sql = 'insert into qiubai values(null,"%s","%s")'%(author,content)
        self.cursor = self.conn.cursor()
        try:
            self.cursor.execute(sql)
            self.conn.commit()
            # 提交事务
        except Exception as e:
            print(e)
            self.conn.rollback()


        self.fp.write(author+':'+content+'\n\n\n')
        return item

```

- 建库 建表

```
mysql -uroot -pxxx

create database qiubai;

use qiubai

create table qiubai (
    id int primary key auto_increment,
    author varchar(100) not null,
    content varchar(500) not null
);
```

> 执行爬虫命令

```
scrapy crawl qiubai --nolog
```

mysql终端里

```
select * from qiubai;
```