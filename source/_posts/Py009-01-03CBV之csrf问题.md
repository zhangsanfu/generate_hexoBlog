---
title: Py009-01-03CBV之csrf问题
date: 2019-02-10 03:40:46
tags: M09
---

### 面试题

> 1.django中间件最多几个

最多五个

- process_request
- process_view
- process_response
- process_exception
- process_render_template

> 2.用中间件做过什么

- 权限
- 用户登录验证
- csrftoken

> 3.django的 csrftoken怎么实现的

- django 里默认中间件 有一个 csrf ，它放在process_view 里（为什么是 ，它放在process_view阶段而不是process_request，因为在响应视图函数时要判断是否存在忽略csrftoken验证的装饰器）
- 而且是全局的，如果注释掉全局都不校验 token

> 4.我想要单独某个不校验 csrftoken 如何实现?

- 装饰器 csrf_exempt 免除csrftoken验证

```
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def users(request):
    user_list = ['alex','aaa','bbb']
    return HttpResponse(json.dumps(user_list))
```

> 5.全局的csrftoken注释了，我想单独给某个视图函数加csrf

```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# 
from django.views.decorators.csrf import csrf_exempt,csrf_protect

@csrf_protect # 该函数需要 csrftoken验证
def users(request):
    user_list = ['alex','aaa','bbb']
    return HttpResponse(json.dumps(user_list))
```

#### CBV忽略csrf

- from django.views.decorators.csrf import csrf_exempt,csrf_protect
- from django.utils.decorators import method_decorator
- 在 dispatch上添加 装饰器 method_decorator
- 在 method_decorator 里传入 csrf_exempt

```
from django.views.decorators.csrf import csrf_exempt,csrf_protect
from django.utils.decorators import method_decorator
class StudentsView(View):
    @method_decorator(csrf_exempt)
    def dispatch(self, request, *args, **kwargs):
        return super(StudentsView,self).dispatch(self,request,*args,**kwargs);
    def get(self,request,*args,**kwargs):
        return HttpResponse('get');

    def post(self,request,*args,**kwargs):
        return HttpResponse('post');

    def put(self,request,*args,**kwargs):
        return HttpResponse('put');

    def delete(self,request,*args,**kwargs):
        return HttpResponse('delete');
```

或者

```
from django.views.decorators.csrf import csrf_exempt,csrf_protect
from django.utils.decorators import method_decorator

@method_decorator(csrf_exempt,name='dispatch')
class StudentsView(View):

    def get(self,request,*args,**kwargs):
        return HttpResponse('get');

    def post(self,request,*args,**kwargs):
        return HttpResponse('post');

    def put(self,request,*args,**kwargs):
        return HttpResponse('put');

    def delete(self,request,*args,**kwargs):
        return HttpResponse('delete');
```