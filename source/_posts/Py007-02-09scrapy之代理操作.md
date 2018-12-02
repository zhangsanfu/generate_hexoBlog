---
title: Py007-02-09scrapy之代理操作
date: 2018-12-02 17:15:35
tags: M07
---

### 代理操作

- https://www.baidu.com/s?wd=ip 

这个url可以看本机ip

#### 代理操作初步——先看本机ip

> 新建工程

```
scrapy startproject proxyPro

cd proxyPro

scrapy genspider proxyDemo www.baidu.com/s?wd=ip
```

>  修改 生成的spiders/proxyDemo.py

```
import scrapy

class ProxydemoSpider(scrapy.Spider):
    name = 'proxyDemo'
    # allowed_domains = ['www.baidu.com/s?wd=ip']
    start_urls = ['https://www.baidu.com/s?wd=ip']

    def parse(self, response):
        fp = open('proxy.html','w',encoding='utf-8')
        fp.write(response.text)
```

> 修改一些配置 settings.py

```
# 伪装请求载体身份
19行：USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36' 

22行：ROBOTSTXT_OBEY = False  #可以忽略或者不遵守robots协议
# 不遵守robots协议
```

> 执行爬虫命令

```
scrapy crawl proxyDemo --nolog

# 查看 proxy.html 看到本机ip
```

#### 代理——中间件

> middlewares.py 的修改

自定义下载中间件的类，在类中事先 process_request (处理中间拦截到的请求的)方法

```
class MyProxy(object):
    # 此时的process_request 依旧是对父类方法的重写
    def process_request(self,request,spider):
        # 请求ip的更换
        '''
        去 代理ip网 找个代理
        http://www.goubanjia.com/
        '''
        request.meta['proxy'] = 'https://123.1.150.244:80'

# 之前其他的无用代码可以移除
```

> 去settings.py里继续配置

```
# 找到 下载中间件 DOWNLOADER_MIDDLEWARES
# 这个是之前 middlewares.py里的 下载中间件
DOWNLOADER_MIDDLEWARES = {
   'proxyPro.middlewares.ProxyproDownloaderMiddleware': 543,
}

# 修改成我们自己定义的 中间件类
DOWNLOADER_MIDDLEWARES = {
   'proxyPro.middlewares.MyProxy': 543,
}
```

> 执行爬虫命令(此时建议  不携带--nolog参数，因为代理ip可能会失败)

```
scrapy crawl proxyDemo 
```

> #### 注意点

```
代理和请求的url的协议头  要一致  要么是http ,要么是https
```

### 总结

> 1.下载中间件类的自定义：

- 继承 object 
- 重写 process_request(self,request,spider) 方法

> 2.配置文件中 开启下载中间件