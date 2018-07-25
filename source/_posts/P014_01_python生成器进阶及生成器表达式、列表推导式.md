---
title: P014_01_python生成器进阶及生成器表达式、列表推导式
date: 2018-07-25 14:23:38
tags: fullstack
---

### 生成器回顾

> 双下方法 ： 很少直接调用的方法。一般情况下，是通过其他语法触发的

#### next()

```
#娃哈哈%i
def wahaha():
    for i in range(2000000):
        yield '娃哈哈%s'%i

# wahaha是一个生成器函数
g = wahaha()

#我们获取它的内容明显不该 这样
# g.__next__()

print(next(g))
print(next(g))
print(next(g))
print(next(g))
```

#### send()

> 

```
def generator():
    print(123)
    content = yield 1
    print('=======',content)
    print(456)
    yield 2

# 返回生成器对象
g = generator() 

# 返回第一个yield 后的值 1
ret = g.__next__()
print('***',ret)

# 给 yield传递一个值
ret = g.send('hello')   #send的效果和next一样
print('***',ret)

结果：
123
*** 1
======= hello
456
*** 2
```

#### send和 next的区别

- 相同点：都可以让生成器向下走一步
- 异同点： send 可以给一个值，这个值作为上一个 yield 的整体的结果返回


> 使用send的注意事项:

1. 第一次使用生成器的时候,要用next获取下一个值
2. 最后一个yield不能接受外部的值

> 为啥第一次不能用send() 

因为会报错！！！详情请看 [为什么python的yield第一次不能用send发送数据？](https://www.zhihu.com/question/28105502)

