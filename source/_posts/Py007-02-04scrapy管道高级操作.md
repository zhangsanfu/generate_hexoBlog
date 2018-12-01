---
title: Py007-02-04scrapy管道高级操作
date: 2018-12-01 23:11:45
tags: M07
---

### 需求：将爬取的数据分别存到本地磁盘/redis数据库/mysql数据库



1. 需要在管道文件 pipelines.py 里编写对应的管道类
2. 在配置文件里 settings.py 对自定义管道类进行生效操作


> 依旧基于之前的qiubaiPro项目

pipelines.py

```
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

# 基于管道  文件存储
# class QiubaiproPipeline(object):
#     fp = None
#     # 在爬虫过程中，该方法只会在开始爬虫的时候调用一次
#     def open_spider(self,spider):
#         print('开始爬虫')
#         self.fp = open('./qiubai_pipe.txt', 'w', encoding='utf-8')
#
#     # 在爬虫过程中，该方法只会在爬虫结束的时候调用一次
#     def close_spider(self, spider):
#         print('结束爬虫')
#         self.fp.close()
#
#     # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
#     '''
#     :param
#     # 参数item:表示接收到的item对象
#     '''
#     def process_item(self, item, spider):
#         # 取出item对应的数据值
#         author = item['author']
#         content = item['content']
#
#         # 持久化存储
#         self.fp.write(author+':'+content+'\n\n\n')
#         return item


# # mysql
# import pymysql
#
# class QiubaiproPipeline(object):
#     conn = None
#     # 在爬虫过程中，该方法只会在开始爬虫的时候调用一次
#     def open_spider(self,spider):
#         print('开始爬虫')
#         # 连接数据库
#         self.conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',password='831015',db='qiubai')
#
#
#     # 在爬虫过程中，该方法只会在爬虫结束的时候调用一次
#     def close_spider(self, spider):
#         print('结束爬虫')
#         self.cursor.close()
#         self.conn.close()
#
#     # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
#     '''
#     :param
#     # 参数item:表示接收到的item对象
#     '''
#     def process_item(self, item, spider):
#         # 取出item对应的数据值
#         author = item['author']
#         content = item['content']
#
#         # 连接数据库
#         # 执行sql
#         sql = 'insert into qiubai values(null,"%s","%s")'%(author,content)
#         self.cursor = self.conn.cursor()
#         try:
#             self.cursor.execute(sql)
#             self.conn.commit()
#             # 提交事务
#         except Exception as e:
#             print(e)
#             self.conn.rollback()
#
#         return item

# redis   安装 pip3 install redis
import redis
import json
class QiubaiproPipeline(object):
    conn = None
    # 在爬虫过程中，该方法只会在开始爬虫的时候调用一次
    def open_spider(self,spider):
        print('开始爬虫')
        # 连接数据库

        self.conn = redis.Redis(host='127.0.0.1', port=6379)

    # 在爬虫过程中，该方法只会在爬虫结束的时候调用一次
    def close_spider(self, spider):
        print('结束爬虫')
        print(self.conn.lrange('data',0,-1))

    # 该方法接收 爬虫文件中提交过来的item对象，并对item对象中存储的数据进行持久化存储
    '''
    :param
    # 参数item:表示接收到的item对象
    '''
    def process_item(self, item, spider):
        # 取出item对应的数据值
        author = item['author']
        content = item['content']

        obj = {
            'author':author,
            'content':content
        }
        # 录入数据
        # self.conn.lpush('data', obj) 5.0版本报错  发现 序列化之后就好了
        self.conn.lpush('data',json.dumps(obj))
        return item


# 封装另外的类  存储操作

# 将数据存入到本地磁盘
class QiubaiByFiles(object):
    def process_item(self,item,spider):
        print('数据写入磁盘')
        return item

# 将数据存入到 Mysql
class QiubaiByMySql(object):
    def process_item(self,item,spider):
        print('数据写入mysql')
        return item


# 将数据存入到 Redis
class QiubaiByRedis(object):
    def process_item(self,item,spider):
        print('数据写入redis')
        return item

```

> ####  如何将自定义的类 加入管道呢？


settings.py里

```
# settings.py里 ITEM_PIPELINES 进行配置

ITEM_PIPELINES = {
   'qiubaiPro.pipelines.QiubaiproPipeline': 300,
   'qiubaiPro.pipelines.QiubaiByFiles': 400,
   'qiubaiPro.pipelines.QiubaiByMySql': 500,
   'qiubaiPro.pipelines.QiubaiByRedis': 600,
}

# 300代表执行的优先级 
# 此时 QiubaiByRedis的优先级最高
```
