---
title: Py007-01-03内置urllib模块补充
date: 2018-11-29 22:59:14
tags: M07
---

### urllib补充

```
# 补充说明：
urlopen函数原型：
    urllib.request.urlopen(url, data=None, timeout=<object object at 0x10af327d0>, *, cafile=None, capath=None, cadefault=False, context=None)

在上一节中我们只使用了该函数中的第一个参数url。在日常开发中，我们能用的只有url和data这两个参数。

url参数：指定向哪个url发起请求
data参数：可以将post请求中携带的参数封装成字典的形式传递给该参数

urlopen函数返回的响应对象，相关函数调用介绍：
response.headers()：获取响应头信息
response.getcode()：获取响应状态码
response.geturl()：获取请求的url
response.read()：获取响应中的数据值（字节类型）
```

#### 二进制数据的爬取

> 爬取网络上的某张图片数据，且存储到磁盘

- 方法 1：

```
import urllib.request
import urllib.parse

#1.指定url
url = 'https://pic.qiushibaike.com/system/pictures/12112/121121212/medium/ZOAND29U4NKNEWEF.jpg'
#2.发起请求:使用urlopen函数发起请求，该函数返回一个响应对象
response = urllib.request.urlopen(url=url)
#3.获取响应对象中的图片二进制类型的数据
img_data = response.read()
#4.持久化存储：将爬取的图片写入本地进行保存
with open('./tupian.png','wb') as fp:
    fp.write(img_data)
```

- 方法 2：

```
import urllib.request
import urllib.parse

url = 'https://pic.qiushibaike.com/system/pictures/12112/121121212/medium/ZOAND29U4NKNEWEF.jpg'
# 函数原型：urllib.request.urlretrieve(url, filename=None)
# 作用：对url发起请求，且将响应中的数据值写入磁盘进行存储
urllib.request.urlretrieve(url=url,filename='./img.png')
```

#### url的特性

url必须为ASCII编码的数据值。所以我们在爬虫代码中编写url时，如果url中存在非ASCII编码的数据值，则必须对其进行ASCII编码后，该url方可被使用。

> 案例：爬取使用搜狗根据指定词条搜索到的页面数据（例如爬取词条为‘周杰伦’的页面数据）

```
import urllib.request
import urllib.parse
url = 'https://www.sogou.com/web?query=周杰伦'
response = urllib.request.urlopen(url=url)
data = response.read()
with open('./周杰伦.html','wb') as fp:
    fp.write(data)
print('写入文件完毕')
```

【注意】上述代码中url存在非ascii编码的数据，则该url无效。如果对其发起请求，则会报如下错误：

```
UnicodeEncodeError: 'ascii' codec can't encode characters in position 15-17: ordinal not in range
```
    
> 所以必须对url中的非ascii的数据进行ascii的编码，则该url方可被发起请求：

- 方法 1：使用quote函数

```
import urllib.request
import urllib.parse
url = 'https://www.sogou.com/web?query=%s'
#对url中的非ascii进行编码.quote函数可以对非ascii的数据值进行ascii的编码
word = urllib.parse.quote('周杰伦')
#将编码后的数据值拼接回url中
url = format(url%word)
response = urllib.request.urlopen(url=url)
data = response.read()
with open('./周杰伦.html','wb') as fp:
    fp.write(data)
print('写入文件完毕')
```

- 方法2： 使用urlencode函数

```
import urllib.request
import urllib.parse
url = 'https://www.sogou.com/web?'
#将get请求中url携带的参数封装至字典中
param = {
    'query':'周杰伦'
}
#对url中的非ascii进行编码
param = urllib.parse.urlencode(param)
#将编码后的数据值拼接回url中
url += param 
response = urllib.request.urlopen(url=url)
data = response.read()
with open('./周杰伦1.html','wb') as fp:
    fp.write(data)
print('写入文件完毕')
```

#### 通过自定义请求对象，用于伪装爬虫程序请求的身份。

之前在讲解http常用请求头信息时，我们讲解过User-Agent参数，简称为UA，该参数的作用是用于表明本次请求载体的身份标识。如果我们通过浏览器发起的请求，则该请求的载体为当前浏览器，则UA参数的值表明的是当前浏览器的身份标识表示的一串数据。如果我们使用爬虫程序发起的一个请求，则该请求的载体为爬虫程序，那么该请求的UA为爬虫程序的身份标识表示的一串数据。有些网站会通过辨别请求的UA来判别该请求的载体是否为爬虫程序，如果为爬虫程序，则不会给该请求返回响应，那么我们的爬虫程序则也无法通过请求爬取到该网站中的数据值，这也是反爬虫的一种初级技术手段。那么为了防止该问题的出现，则我们可以给爬虫程序的UA进行伪装，伪装成某款浏览器的身份标识。


上述案例中，我们是通过request模块中的urlopen发起的请求，该请求对象为urllib中内置的默认请求对象，我们无法对其进行UA进行更改操作。urllib还为我们提供了一种自定义请求对象的方式，我们可以通过自定义请求对象的方式，给该请求对象中的UA进行伪装（更改）操作。

```
import urllib.request
import urllib.parse
url = 'https://www.sogou.com/web?'
#将get请求中url携带的参数封装至字典中
param = {
    'query':'周杰伦'
}
#对url中的非ascii进行编码
param = urllib.parse.urlencode(param)
#将编码后的数据值拼接回url中
url += param 

#封装自定义的请求头信息的字典：
#将浏览器的UA数据获取，封装到一个字典中。该UA值可以通过抓包工具或者浏览器自带的开发者工具中获取某请求，从中获取UA的值
#注意：在headers字典中可以封装任意的请求头信息
headers={
    'User-Agent' : 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36'
    }
#自定义请求对象，可以在该请求对象中添加自定义的请求头信息
request = urllib.request.Request(url=url,headers=headers)
#使用自定义请求对象发起请求
response = urllib.request.urlopen(request)
data = response.read()
with open('./周杰伦.html','wb') as fp:
    fp.write(data)
print('写入文件完毕')
```

#### 携带参数的post请求

> 案例：百度翻译发起post请求

```
import urllib.request
import urllib.parse
#通过抓包工具抓取post请求的url
post_url='https://fanyi.baidu.com/sug'
#封装post请求参数
data={
    "kw":"dog"
}
data=urllib.parse.urlencode(data)
#自定义请求头信息字典
headers={
    "User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.87 Safari/537.36"
}

#自定义请求对象，然后将封装好的post请求参数赋值给Requst方法的data参数。
#data参数：用来存储post请求的参数
request=urllib.request.Request(post_url,data=data.encode(),headers=headers)
#自定义的请求对象中的参数（data必须为bytes类型）
response=urllib.request.urlopen(request)
response.read()
```

### urllib模块的高级操作

> 1. 代理

- 什么是代理：代理就是第三方代替本体处理相关事务。例如：生活中的代理：代购，中介，微商......

- 爬虫中为什么需要使用代理？

    > 一些网站会有相应的反爬虫措施，例如很多网站会检测某一段时间某个IP的访问次数，如果访问频率太快以至于看起来不像正常访客，它可能就会会禁止这个IP的访问。所以我们需要设置一些代理IP，每隔一段时间换一个代理IP，就算IP被禁止，依然可以换个IP继续爬取。

- 代理的分类：

正向代理：代理客户端获取数据。正向代理是为了保护客户端防止被追究责任。

反向代理：代理服务器提供数据。反向代理是为了保护服务器或负责负载均衡。

```
import urllib.request
import urllib.parse

#1.创建处理器对象，在其内部封装代理ip和端口
handler=urllib.request.ProxyHandler(proxies={'http':'95.172.58.224:52608'})
#2.创建opener对象，然后使用该对象发起一个请求
opener=urllib.request.build_opener(handler)

url='http://www.baidu.com/s?ie=UTF-8&wd=ip'

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.181 Safari/537.36',
}

request = urllib.request.Request(url, headers=headers)

#使用opener对象发起请求，该请求对应的ip即为我们设置的代理ip
response = opener.open(request)

with open('./daili.html','wb') as fp:
    fp.write(response.read())
```

>  2.cookie

引言：有些时候，我们在使用爬虫程序去爬取一些用户相关信息的数据（爬取张三“人人网”个人主页数据）时，如果使用之前requests模块常规操作时，往往达不到我们想要的目的，例如：

```
import urllib.request
import urllib.parse
#指定url
url = 'http://www.renren.com/289676607/profile'
#自定义请求头信息
headers={
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
    }
#自定义请求对象
request = urllib.request.Request(url=url,headers=headers)
#发起请求
response = urllib.request.urlopen(request)

with open('./renren.html','w') as fp:
    fp.write(response.read().decode())
```

【注意】上述代码中，我们爬取到的是登录首页面，而不是张三的个人主页也面。why？首先我们来回顾下cookie的相关概念及作用

        - cookie概念：当用户通过浏览器首次访问一个域名时，访问的web服务器会给客户端发送数据，以保持web服务器与客户端之间的状态保持，这些数据就是cookie。

        - cookie作用：我们在浏览器中，经常涉及到数据的交换，比如你登录邮箱，登录一个页面。我们经常会在此时设置30天内记住我，或者自动登录选项。那么它们是怎么记录信息的呢，答案就是今天的主角cookie了，Cookie是由HTTP服务器设置的，保存在浏览器中，但HTTP协议是一种无状态协议，在数据交换完毕后，服务器端和客户端的链接就会关闭，每次交换数据都需要建立新的链接。就像我们去超市买东西，没有积分卡的情况下，我们买完东西之后，超市没有我们的任何消费信息，但我们办了积分卡之后，超市就有了我们的消费信息。cookie就像是积分卡，可以保存积分，商品就是我们的信息，超市的系统就像服务器后台，http协议就是交易的过程。 

        - 经过cookie的相关介绍，其实你已经知道了为什么上述案例中爬取到的不是张三个人信息页，而是登录页面。那应该如何抓取到张三的个人信息页呢？

　　思路：

　　　　1.我们需要使用爬虫程序对人人网的登录时的请求进行一次抓取，获取请求中的cookie数据

　　　　2.在使用个人信息页的url进行请求时，该请求需要携带 1 中的cookie，只有携带了cookie后，服务器才可识别这次请求的用户信息，方可响应回指定的用户信息页数据

```
cookiejar对象：
    - 作用：自动保存请求中的cookie数据信息
    - 注意：必须和handler和opener一起使用
cookiejar使用流程：
    - 创建一个cookiejar对象
      import http.cookiejar
      cj = http.cookiejar.CookieJar()
    - 通过cookiejar创建一个handler
      handler = urllib.request.HTTPCookieProcessor(cj)
    - 根据handler创建一个opener
      opener = urllib.request.build_opener(handler)
    - 使用opener.open方法去发送请求，且将响应中的cookie存储到openner对象中，后续的请求如果使用openner发起，则请求中就会携带了cookie
```

使用cookiejar实现爬取人人网个人主页页面数据：

```
#使用cookiejar实现人人网的登陆
import urllib.request
import urllib.parse
import http.cookiejar
cj = http.cookiejar.CookieJar() #请求中的cookie会自动存储到cj对象中
#创建处理器对象(携带cookiejar对象的)
handler=urllib.request.HTTPCookieProcessor(cj)
#创建opener对象 （携带cookiejar对象）
opener=urllib.request.build_opener(handler)

#要让cookiejar获取请求中的cookie数据值
url='http://www.renren.com/ajaxLogin/login?1=1&uniqueTimestamp=201873958471'
#自定义一个请求对象，让该对象作为opener的open函数中的参数
data={
    "email":"www.zhangbowudi@qq.com",
    "icode":"",
    "origURL":"http://www.renren.com/home",
    "domain":"renren.com",
    "key_id":"1",
    "captcha_type":"web_login",
    "password":"40dc65b82edd06d064b54a0fc6d202d8a58c4cb3d2942062f0f7dd128511fb9b",
    "rkey":"41b44b0d062d3ca23119bc8b58983104",
  
 'f':"https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3DpPKf2680yRLbbZMVdntJpyPGwrSk2BtpKlEaAuKFTsW%26wd%3D%26eqid%3Deee20f380002988c000000025b7cbb80"
}
data=urllib.parse.urlencode(data).encode()
request=urllib.request.Request(url,data=data)
opener.open(request)

#获取当前用户的二级子页面
s_url='http://www.renren.com/289676607/profile'
#该次请求中就携带了cookie
resonse=opener.open(s_url)

with open('./renren.html','wb') as fp:
    fp.write(resonse.read())

```


