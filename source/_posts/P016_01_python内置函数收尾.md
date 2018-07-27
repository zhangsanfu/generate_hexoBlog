---
title: P016_01_python内置函数收尾
date: 2018-07-26 21:54:31
tags: fullstack
---

### python中一个让你懵的面试题

> 数据类型有哪些？

- int
- bool

> 数据结构

- dict
- list
- tuple
- set
- str

### 剩余内置函数

> reversed() 保留原列表，返回一个反向的迭代器

```
l = [1,2,3,4,5]
l.reverse()
print(l) # 此时 l 还是那个列表只不过内容反序了

l = [1,2,3,4,5]
l2 = reversed(l)  # 保留 l 不变 ，返回一个反序的迭代器
print(l2)
```

> slice() 切片方法

```
l = (1,2,23,213,5612,342,43)
sli = slice(1,5,2)
print(l[sli])
print(l[1:5:2])
```

#### 字符串相关内置方法

> format()

```
print(format('test', '<20')) # 开辟20个字符空间 左对齐
print(format('test', '>40')) # 开辟40个字符空间 右对齐
print(format('test', '^40')) # 开辟40个字符空间 居中对齐

结果：
test                
                                    test
                  test 
```

> bytes() 把unicode转换成 「对应编码」 的bytes

```
# 我拿到的是gbk编码的，我想转成utf-8编码
print(bytes('你好',encoding='GBK'))     # unicode转换成GBK的bytes
print(b'\xc4\xe3\xba\xc3'.decode(encoding='GBK'))
print(bytes('你好',encoding='utf-8'))   # unicode转换成utf-8的bytes
print(b'\xe4\xbd\xa0\xe5\xa5\xbd'.decode(encoding='utf-8'))
结果：
b'\xc4\xe3\xba\xc3'   gbk编码的byte编码
你好    decode按照gbk解码后的unicode字符
b'\xe4\xbd\xa0\xe5\xa5\xbd'   utf-8编码的byte编码
你好    decode按照utf-8解码后的unicode字符
```

> bytearry()

- 网络编程 只能传二进制
- 照片和视频也是以二进制存储
- html网页爬取到的也是编码

```
b_array = bytearray('你好',encoding='utf-8') # unicode 你好转换为 utf-8编码的 byte数组
print(b_array)
print(len(b_array))
print(b_array[0])

结果：
bytearray(b'\xe4\xbd\xa0\xe5\xa5\xbd')
6
228
```

> memoryview (非重点知识点)

- 将数据转换为指定编码的byte之后进行一次memoryview就可以进行切片,同时：这个切片不创建新的,而且这个切片是字节类型的

[详情请参考 https://segmentfault.com/q/1010000007137721](https://segmentfault.com/q/1010000007137721)

也可以看我的摘录上述链接的信息在 [P016_01_python内置函数收尾]

> ord() 字符按照unicode转数字

```
print(ord('好')) # 22909
print(ord('a'))  # 97
print(ord('1'))  # 49
```

> chr() 将数字转字符

```
chr(97) # a
```

> ascii() 

只要是ascii码中的内容，就打印出来，不是就转换成\u


> #### repr() 原样输出

用于%r格式化输出(%r实际就是调用repr())

```
name = 'egg'
print('你好%r'%name)
print(repr('1'))
print(repr(1))

结果：
你好'egg'
'1'
1
```

#### 数据集合相关方法

1. 字典：

- dict

2. 集合：

- set：无序排序且不重复，是可变的。
- frozenset：是冻结的集合，他是不可变的，存在哈希值。可以作为字典的key

