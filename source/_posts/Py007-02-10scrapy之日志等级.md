---
title: Py007-02-10scrapy之日志等级
date: 2018-12-02 17:48:01
tags: M07
---

### 日志

回顾我们之前的爬虫命令

```
scrapy crawl 应用名  --nolog

# 此时的打印信息  很多完全看不懂！
```

#### 日志等级(种类)

- ERROR:错误
- WARNING:警告
- INFO:一般信息
- DEBUG:调试信息(默认)

> #### 控制日志的打印 settings.py

- LOG_LEVEL 指定输出某一种信息
- LOG_FILE 将日志信息存到一个文件里

```
# 添加 LOG_LEVEL = '种类' 来控制显示哪一层级的日志
LOG_LEVEL = 'ERROR'
# 此时只有错误的时候才会打印日志
```

> 将日志信息存到一个文件里

```
LOG_FILE = 'log.txt'
```


