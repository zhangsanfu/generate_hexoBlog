---
title: DOM事件
date: 2017-12-01 15:39:47
tags: DOM
---

#### 浏览器

> 网景 Navigator ==> Firefox

> 微软 IE        ==> Edge

## dom level-0
```
<button onclick="a">A</button>
<button onclick="a()">B</button>
<button onclick="a.call()">C</button>

<script>
    function a(){
        console.log(1);
    }
//ABC按钮那个会触发  答案是BC
</script>
```

```
<button id="x" >A</button>
<button id="y" >B</button>
<button id="z" >C</button>

<script>
    function a(){
        console.log(1);
    }
    x.onclick = a;
    y.onclick = a();
    z.onclick = a.call();
    
//ABC按钮那个会触发  答案是 A 
</script>
```
#### 标签上onclick
```
<button onclick="a()">A</button>
//onclick ="要执行的代码"
//一旦用户点击 浏览器就eval('要执行的代码')
```

#### js里的 onclick
```
function a(){console.log(1)}
x.onclick = 类型为函数对象
x.onclick = a;
x.onclick = a(); //undefined
x.onclick = a.call()//undefined

```

## dom level-2
```
//addEventListener
xxx.addEventListener('click',function(){
    console.log()
})
```
> 看似addEventListener() 反而比以前更麻烦了  实际不是这样的

> onclick是属性、唯一 重复声明就会后面的覆盖前面的
```
xxx.onclick = function(){
    alert(1)
}
xxx.onclick = function(){
    alert(2)
}
// 弹出 2

```
#### addEventListener 是一个队列  事件监听队列
```
xxx.addEventListener('click',function(){alert(1)});

xxx.addEventListener('click',function(){alert(2)});

//由于是队列所以就会依次触发
// 弹一  
// 弹二
```

> 既然可以进入队列 就一定可以出队 removeEventListener()
```
function f1(){console.log(1)}
function f2(){console.log(2)}
function f3(){console.log(3)}

xxx.addEventListener('click',f1);

xxx.addEventListener('click',f2);

xxx.removeEventListener('click',f1);

xxx.addEventListener('click',f3);

xxx.removeEventListener('click',f3);

//当我点击 xxx的时候打印什么

//打印  2

```

> 如何实现one的效果  就执行一次之后就不触发了
```
function f1(){
    console.log(1)
    //执行之后从事件队列移除
    xxx.removeEventListener('click',f1);
}

xxx.addEventListener('click',f1);  //点击的时候执行

```

### 问题1  当我点击儿子的时候，我是否点击了 爸爸和爷爷 ==》yes
```
<div id="grand1">
    爷爷
    <div id="parent1">
        爸爸
        <div id="child1">
            儿子
        </div>
    </div>
</div>

```
### 问题2 当我点击儿子的时候，三个函数是否调用 ==> yes
```
grand1.addEventListener('click',function(){
  console.log('爷爷')
})

parent1.addEventListener('click',function(){
  console.log('爸爸')
})

child1.addEventListener('click',function(){
  console.log('儿子')
})

```

### 问题3 三个函数的执行顺序 ==> 1 2 3 or 3 2 1 答案是都可以
> addEventListener('事件',函数,可选参数) 

> 第三个参数是 boolean值  不传代表false : 0 NaN undefined null false ''

> false的话 顺序从内到外 儿子  爸爸  爷爷
 
> true 的话 顺序从外到内 爷爷  爸爸  儿子

> ![推荐](https://raw.githubusercontent.com/slTrust/note/master/img/note032_0.png)

> 爷爷设置true 其他设置false 

```
grand1.addEventListener('click',function(){
  console.log('爷爷')
},true)

parent1.addEventListener('click',function(){
  console.log('爸爸')
})

child1.addEventListener('click',function(){
  console.log('儿子')
})
// 爷爷  儿子  爸爸
```

### 考点 如果  儿子节点既有捕获又有冒泡执行顺序是什么
```
child1.addEventListener('click',function(){
  console.log('儿子捕获')
},true)

child1.addEventListener('click',function(){
  console.log('儿子冒泡')
},false)

// 儿子捕获
// 儿子冒泡
```

```
child1.addEventListener('click',function(){
  console.log('儿子冒泡')
},false)

child1.addEventListener('click',function(){
  console.log('儿子捕获')
},true)


// 儿子冒泡
// 儿子捕获
```
> 答案是 谁写在前面谁先触发

### 事件模型应用 点击显示浮层 点击其它地方关闭浮层

> 问题1 点击按钮浮层不出现
```
<div id="wrapper" class="wrapper">
    <button id="clickMe">点我</button>
    <div id="popover" class="popover">
      浮层
    </div>
</div>
<script>
    //点击显示浮层 
    clickMe.addEventListener('click', function(){
        popover.style.display = 'block';
    })

    document.addEventListener('click', function(){
        popover.style.display = 'none'
    })
</script>
```
> 点击按钮的时候浮层确实出现了 

> 但是addEventListener('事件',fn,布尔值) 第三个参数不传代表false 代表冒泡

> 冒泡到document  

> document 发现事件队列里有 fn于是执行   popover.style.display = 'none'

### 如何解决 ==> event.stopPropagation(); 
> Prevents further propagation of the current event in the capturing and bubbling phases.

> 防止当前事件在捕获和冒泡阶段的进一步传播。

> ##### 早一点的前端肯定会认为还有一个方法阻止冒泡 event.cancelBubble = true ;不好意思 该属性已废弃

```
//点击显示浮层 
clickMe.addEventListener('click', function(event){
    popover.style.display = 'block';
    event.stopPropagation(); //这样冒泡阶段就被禁止了 
})

document.addEventListener('click', function(){
    popover.style.display = 'none'
})
```

### 问题2 虽然解决了阻止冒泡 但是当你点击浮层时  浮层也会消失

> 这样就不能在按钮上阻止冒泡 因为点击浮层也会冒泡到wrapper
- 因为btn会冒泡到 wrapper 
- popover会冒泡到 wrapper
- 所以在它们的父节点做阻止冒泡

```
<div id="wrapper" class="wrapper">
    <button id="clickMe">点我</button>
    <div id="popover" class="popover">
        浮层
    </div>
</div>
<script>
clickMe.addEventListener('click', function(e){
    popover.style.display = 'block';
})
wrapper.addEventListener('click',function(e){
    //解决点击浮层触发的冒泡
    e.stopPropagation()
})

document.addEventListener('click', function(){
    popover.style.display = 'none'
})
</script>
```

### 问题3 现在点击按钮浮层出现  点击浮层浮层浮层不消失 但是有性能问题
- 100个浮层就是100个事件队列  10000个呢？

> 现在我们先换jq来实现刚刚的效果

> ### jq提供了一个非常方便的方法  $(node).on('click',false) 第二个参数代表 阻止默认事件 和阻止传播

### 又偶生出一个bug 就是.on('事件',false) 你在checkbox外的任何一层 阻止默认事件 这个checkbox就无法选中

```
<div id="wrapper" class="wrapper">
    <button id="clickMe">点我</button>
    <div id="popover" class="popover">
        <input type="checkbox">浮层
    </div>
</div>


$(clickMe).on('click',function(){
    $(popover).show();
});
// 这样会再次出bug就是  如果浮层里有checkbox就无法选中 因为阻止了默认事件 
$(wrapper).on('click',false)  

$(document).on('click',function(){
    $(popover).hide();
})

```

> 所以我们还是单独阻止传播 

```
$(clickMe).on('click',function(){
    $(popover).show();
});

$(wrapper).on('click',function(e){
    e.stopPropagation(); //阻止冒泡
});  

$(document).on('click',function(){
    $(popover).hide();
})
```

### 解决问题3 性能问题
- 点击按钮的时候显示浮层 同时添加一个document监听
- 为什么监听不会触发 因为 wrapper哪里阻止了 冒泡
```
$(clickMe).on('click',function(){
    $(popover).show();
    //只有显示的时候监听一次 但是不会触发  因为 wrapper上阻止了冒泡
    //这样我只监听一次 click 同时马上干掉这个监听事件   
    $(document).one('click',function(){
      console.log('doc')
      $(popover).hide();
    })
});

//所以只能这样写  单独阻止它的冒泡 不阻止默认事件
$(wrapper).on('click',function(e){
    e.stopPropagation(); //阻止冒泡
});
```

### 问题4 如果不在wrapper上不阻止冒泡 点击btn为什么浮层不显示
```
$(clickMe).on('click',function(){
    $(popover).show();
    //只有显示的时候监听一次 但是不会触发  因为 wrapper上阻止了冒泡
    //这样我只监听一次 click 同时马上干掉这个监听事件   
    $(document).one('click',function(){
      console.log('doc')
      $(popover).hide();
    })
});
```
- 在你点击btn的时候它会做两件事
- 1 浮层显示出来
- 2 document的事件队列里 添加一个函数
- 3 事件开始冒泡
- 4 开始发现 document的事件队列里有一个函数
- 5 执行 document事件队列里的函数
- 6 浮层隐藏了

