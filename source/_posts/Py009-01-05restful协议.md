---
title: Py009-01-05restful协议
date: 2019-02-10 14:03:05
tags: M09
---

### restful协议

> #### 1.API与用户的通信协议，推荐使用 HTTPS

> #### 2.域名

- 子域名方式
    ```
    www.luffycity.com
    api.luffycity.com

    存在跨域，还要买域名
    ```
- URL方式（推荐）
    ```
    www.luffycity.com
    www.luffycity.com/api/    

    不存在跨域，便宜
    ```

> #### 3.版本

- URL 如 www.luffycity.com/api/v1/
- 请求头

> #### 4.路径，视网络任何东西都是资源，均用名词表示

- www.luffycity.com/api/v1/user/ 
- www.luffycity.com/api/v1/book/
- www.luffycity.com/api/v1/teacher/  

```
URL 推荐 www.luffycity.com/api/v1/名词/

# 增删改查这种动词不该出现在 url 里
www.luffycity.com/api/v1/user/     正确格式
www.luffycity.com/api/v1/get_user/ 没遵守规范

```

> #### 5.method

- get 从服务器获取资源(一项或多项)
- post 新建一个资源
- put 在服务器更新资源(客户端提供改变后的完整资源)全部更新
- patch 在服务器更新资源(客户端提供改变的属性)局部更新
- delete 从服务器删除资源

> #### 6.过滤，通过url传参的形式传递条件

- www.luffycity.com/api/v1/user?limit=10
- www.luffycity.com/api/v1/user?offset=10
- www.luffycity.com/api/v1/user?page=2&page_size=10
- www.luffycity.com/api/v1/user?sortby=name&order=asc
- www.luffycity.com/api/v1/user?user_type_id=1

> #### 7.状态码

```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

更多看这里：http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html
```

通常结合code一起使用

```
因为状态码有限，而code可以任意定制

{
    code:1004,
    msg:'用户不存在'
}

{
    code:2004,
    msg:'用户已禁用'
}
```

> #### 8.错误处理，状态码是4xx时，应返回错误信息，error当做key。

```
{
    error: "Invalid API key"
}
```

> #### 9.返回结果，针对不同操作，服务器向用户返回的结果应该符合以下规范。
```
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档

# 例子

GET /user 返回资源对象的列表（数组）
GET /user/id 返回单个资源对象
POST /user 返回新生成的资源对象
PUT /user/id 返回完整的资源对象(全部修改)
PATCH /user/id 返回完整的资源对象（局部修改）
DELETE /user/id 返回一个空文档
```

> #### 10.Hypermedia API，RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。


```
/order/
[
    {
        id:1,
        name:'apple',
        url:'http://www.xxx.com/1' # 资源的详细信息
    },
    {
        id:2,
        name:'orange',
        url:'http://www.xxx.com/2' # 资源的详细信息
    }
]

# 免去你单独拼接 url 访问资源
```

### 谈谈你对 restful api 规范的认识

```
本质就是一个api, 定义一些规范，目的就是为了更好的让后台处理和前端方便记住这些url.

其实就是url上体现对api的操作 
在以前如 get/post 也都可以实现这些 crud 但是要声明很多 url  
http://www.xxx.com/get_user/
http://www.xxx.com/update_user/
http://www.xxx.com/add_user/
http://www.xxx.com/delete_user/

前端遇到跨域问题就导致发两次请求 一次 option 一次 get
设置 cors

除了这个跨域还有没其他方式解决 => jsonp

状态码都记得什么
2系列
3系列
4系列
5系列

和上面总结的十条规则

协同开发时更加的统一
```

> #### 如果你写接口推荐使用 CBV的方式