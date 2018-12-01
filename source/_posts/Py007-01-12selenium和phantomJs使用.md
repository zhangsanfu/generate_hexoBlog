---
title: Py007-01-12selenium使用
date: 2018-12-01 11:12:18
tags: M07
---

### selenium使用

- 问题：处理页面动态加载数据的爬取

1. selenium
2. phantomJs

#### selenium安装

三方库，可以实现让浏览器完成自动化操作

- 环境搭建

```
# 1 安装依赖
pip install selenium
# 2 获取某一款浏览器的驱动程序（以谷歌浏览器为例）
# 谷歌浏览器驱动下载地址：http://chromedriver.storage.googleapis.com/index.html
# 
下载的驱动程序必须和浏览器的版本统一，大家可以根据http://blog.csdn.net/huilan_same/article/details/51896672
中提供的版本映射表进行对应
```

> #### 代码介绍

```
#导包
from selenium import webdriver  
#创建浏览器对象，通过该对象可以操作浏览器
browser = webdriver.Chrome('驱动路径')
#使用浏览器发起指定请求
browser.get(url)

#使用下面的方法，查找指定的元素进行操作即可
    find_element_by_id            根据id找节点
    find_elements_by_name         根据name找
    find_elements_by_xpath        根据xpath查找
    find_elements_by_tag_name     根据标签名找
    find_elements_by_class_name   根据class名字查找
```

> 自动化打开百度搜索关键字后页面

```
# 编码流程
from selenium import webdriver
from time import sleep
# 创建一个浏览器对象 executable_path为驱动的路径
bro = webdriver.Chrome(executable_path='./chromedriver')
# get 方法可以指定一个url，让浏览器进行请求
bro.get('https://www.baidu.com')
sleep(3)
# 让百度根据指定 搜索词进行搜索
text = bro.find_element_by_id('kw') # 定位文本框
text.send_keys('abc') # 向文本框录入指定内容
sleep(1)
button = bro.find_element_by_id('su')
button.click() # 表示点击操作
sleep(3)

bro.quit()# 退出浏览器
```

### phantomJs(不推荐使用，官网已经停更了)

- 下载

```
http://phantomjs.org/download.html
```

PhantomJS是一款无界面的浏览器，其自动化操作流程和上述操作谷歌浏览器是一致的。由于是无界面的，为了能够展示自动化操作流程，PhantomJS为用户提供了一个截屏的功能，使用save_screenshot函数实现。
代码演示：

```
#!/usr/bin/env python
#-*- encoding:utf-8 -*-

from selenium import webdriver
import time

# phantomjs路径
path = r'/Users/almost/Downloads/phantomjs-2.1.1-macosx/bin/phantomjs'
browser = webdriver.PhantomJS(path)

# 打开百度
url = 'http://www.baidu.com/'
browser.get(url)

time.sleep(3)

browser.save_screenshot(r'phantomjs\baidu.png')

# 查找input输入框
my_input = browser.find_element_by_id('kw')
# 往框里面写文字
my_input.send_keys('美女')
time.sleep(3)
#截屏
browser.save_screenshot(r'phantomjs\meinv.png')

# 查找搜索按钮
button = browser.find_elements_by_class_name('s_btn')[0]
button.click()

time.sleep(3)

browser.save_screenshot(r'phantomjs\show.png')

time.sleep(3)

browser.quit()
```


#### 重点

> selenium+phantomjs 就是爬虫终极解决方案:有些网站上的内容信息是通过动态加载js形成的，所以使用普通爬虫程序无法回去动态加载的js内容。例如豆瓣电影中的电影信息是通过下拉操作动态加载更多的电影信息。

> #### 综合操作：需求是尽可能多的爬取豆瓣网中的电影信息

```
from selenium import webdriver
from time import sleep
import time

if __name__ == '__main__':
    url = 'https://movie.douban.com/typerank?type_name=%E6%81%90%E6%80%96&type=20&interval_id=100:90&action='
    # 发起请求前，可以让url表示的页面动态加载出更多的数据
    path = r'你的phantomjs路径'
    # 创建无界面的浏览器对象
    bro = webdriver.PhantomJS(path)
    # 发起url请求
    bro.get(url)
    time.sleep(3)
    # 截图
    bro.save_screenshot('1.png')

    # 执行js代码（让滚动条向下偏移n个像素（作用：动态加载了更多的电影信息））
    js = 'window.scrollTo(0,document.body.scrollHeight)'
    bro.execute_script(js)  # 该函数可以执行一组字符串形式的js代码
    time.sleep(2)

    bro.execute_script(js)  # 该函数可以执行一组字符串形式的js代码
    time.sleep(2)
    bro.save_screenshot('2.png') 
    time.sleep(2) 
    # 使用爬虫程序爬去当前url中的内容 
    html_source = bro.page_source # 该属性可以获取当前浏览器的当前页的源码（html） 
    with open('./source.html', 'w', encoding='utf-8') as fp: 
        fp.write(html_source) 
    bro.quit()
```


> #### 谷歌无头浏览器

由于PhantomJs最近已经停止了更新和维护，所以推荐大家可以使用谷歌的无头浏览器，是一款无界面的谷歌浏览器。

- 代码展示：

```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time
 
# 创建一个参数对象，用来控制chrome以无界面模式打开
chrome_options = Options()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
# 驱动路径
path = r'你的路径chromedriver.exe'
 
# 创建浏览器对象
browser = webdriver.Chrome(executable_path=path, chrome_options=chrome_options)
 
# 上网
url = 'http://www.baidu.com/'
browser.get(url)
time.sleep(3)
 
browser.save_screenshot('baidu.png')
 
browser.quit()
```
