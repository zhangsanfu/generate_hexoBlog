---
title: React入门001之React诞生
date: 2019-01-09 22:41:19
tags: React入门
---

### 简单的 add / reduce 需求

- 实现 页面显示 一个数字 
- 点击add +1 
- 点击reduce -1

代码如下：

```
<!DOCTYPE html>
<html lang="en">
<body>
    <div>
        <span id="result">0</span>
        <button id="add">+</button>
        <button id="minus">-</button>
    </div>
</body>
<script>
    let result = document.querySelector('#result');
    let add = document.querySelector('#add');
    let minus = document.querySelector('#minus');

    add.addEventListener('click',function(){
        let number = parseInt(result.innerText,10);
        number += 1;
        result.innerText = number;
    })

    minus.addEventListener('click',function(){
        let number = parseInt(result.innerText,10);
        number -= 1;
        result.innerText = number;
    })
</script>
</html>
```

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study001-01.png)

> 如上代码无论如何优化,本质没有改变，过程如下

- 获取节点元素的值,js中修改元素的值
- 获取节点元素的值,js中回填修改后的值

> 如果是你呢？

- 要么把获取值(num = xx.innerText)干掉 (不去获取了，直接更新dom)
- 要么把回填值(xx.innerText = xxx)干掉 (不现实，这样就js无法通知页面了)

### React诞生

> ### React的思路

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study001-02.png)

- 在一开始页面就什么都没有
- 先声明一个变量叫 number=0
- js里生成 一个 <span>0</span>(虚拟的span) 并把它同步到页面中   <span>0</span>(真实的span)
- number+=1
- 此时重新生成一个 <span>1</span>(虚拟的span) 并把它同步到页面中   <span>1</span>(真实的span)

> 此时永远不需要去页面获取 number了，同时页面里显示的数字永远是我number的最新值

> 实现我们刚刚的需求

- 在 https://www.bootcdn.cn/ 里 搜索react
- 添加 umd格式的react   https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js
- 添加 umd格式react-dom  https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <div id="root">
    </div>
    <button id="add">+</button>

    <script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
    <script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>
    <script>
        let number = 0 ;

        let span = React.createElement('span',{className:'red'},number);
        ReactDOM.render(span,document.querySelector('#root'))

        let add = document.querySelector('#add');

        add.addEventListener('click',function(){
            number += 1;

            let span = React.createElement('span',{className:'red'},number);
            ReactDOM.render(span,document.querySelector('#root'))
        })
    </script>
</body>
</html>
```

> 现在我们的span没有任何的获取元素,只有从虚拟的span到 页面真实的span

优化一下

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <div id="root">
    </div>
    <button id="add">+</button>

    <script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
    <script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>
    <script>
        let number = 0 ;

        render()

        let add = document.querySelector('#add');

        add.addEventListener('click',function(){
            number += 1;
            render()
        })

        function render(){
            let span = React.createElement('span',{className:'red'},number);
            ReactDOM.render(span,document.querySelector('#root'))
        }
    </script>
</body>
</html>
```

再也不用 querySelector后 取它爸爸取它儿子了

> ### 再次优化，按钮也在render里

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <div id="root">
    </div>

    <script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
    <script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>
    <script>
        let number = 0 ;
        let onClickButton = ()=>{
            number += 1;
            render();
        }
        let onClickButton2 = ()=>{
            number -= 1;
            render();
        }
        render()

        function render(){
            let span = React.createElement('span',{className:'red'},number);
            let button = React.createElement('button',{onClick:onClickButton},'+');
            let button2 = React.createElement('button',{onClick:onClickButton2},'-');
            let div = React.createElement('div',{className:'container'},span,button,button2);
            ReactDOM.render(div,document.querySelector('#root'))
        }
    </script>
</body>
</html>
```

> 千万不要以为 render的时候是重新造了一个 dom

- react没那么傻
- 它是局部刷新, 不会整个更新
    > 点击add后 虚拟的 <span>1</span> 会和 页面里<span>0</span> 比较后发现只变了 中间部分的文案 其他没变

### JSX的发明

> #### 第一次优化

```
function render(){
    let h = React.createElement;
    let span = h('span',{className:'red'},number);
    let button = h('button',{onClick:onClickButton},'+');
    let button2 = h('button',{onClick:onClickButton2},'-');
    let div = h('div',{className:'container'},
                    span,
                    button,
                    button2
                );
    ReactDOM.render(div,document.querySelector('#root'))
}

```

> #### 第二次优化，移除临时变量,直接用函数嵌套

```
function render(){
    let h = React.createElement;
    let div = h('div',{className:'container'},
                h('span',{className:'red'},number),
                h('button',{onClick:onClickButton},'+'),
                h('button',{onClick:onClickButton2},'-')
            );
    ReactDOM.render(div,document.querySelector('#root'))
}
```



> #### 第三次优化(最重要的优化)

此时的解构已经蕴含了 html解构

```
h('div',{className:'container'},
    h('span',{className:'red'},number),
    h('button',{onClick:onClickButton},'+'),
    h('button',{onClick:onClickButton2},'-')
);

<div class="container">
    <span class="red">number</span>
    <button onClick=onClickButton>+</button>
    <button onClick=onClickButton2>-</button>
</div>
```

> React开发者惊讶的发现自己代码和html没什么区别

- 想能不能以这种形式写代码，然后用程序翻译成上面的代码呢？

> ### JSX语法

用html的形式写js,终于这种形式被babel采纳了(招安)

```
// 所有变量的地方====> { 变量 }
<div class="container">
    <span class="red"> { number } </span>
    <button onClick= { onClickButton } >+</button>
    <button onClick= { onClickButton2 } >-</button>
</div>

// 以上代码被翻译为
React.createElement(
    "div",
    { class:"container" },
    React.createElement("span",{ class:"red"},number),
    React.createElement("button",{ onClick: onClickButton},"+"),
    React.createElement("button",{ onClick: onClickButton2},"-")
)
```

> #### 但是，我们这样写是运行不了的，有两种方法可以支持我们这样写

- webpack
- 用jsbin

```
// 注意 render2是无法运行的  必须webpack 编译 或者 用jsbin 添加jsx模式
function render2(){
    ReactDOM.render(
        <div class="container">
            <span class="red"> { number } </span>
            <button onClick= { onClickButton } >+</button>
            <button onClick= { onClickButton2 } >-</button>
        </div>,
        document.querySelector('#root')
    )
}
```

> #### 很多人疑惑为什么 onClickButton 不加()呢？

- 加()代表这个 函数的返回值传递给 onClick

```
onClick= { onClickButton }
```

> 再次回到之前那张图

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study001-01.png)

- 我们以前不管用原生js还是 jquery都是 取对应元素节点 获取节点的值,然后对值进行一些逻辑处理，然后回填页面



> React的思路

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study001-02.png)

- 页面你先给我空着
- 所有的东西都通过对象表示，然后把对象放到页面里，你想修改就直接改值，然后更新下这个对象
- 就是 改值 render / 改值 render / 改值 render

> #### 由于 dom API太难用了

- React不择手段的做到：不去访问dom,只用你更新数据，构造出一个虚拟dom(就是不是真实的dom)

### 总结

- 虚拟dom就是一个对象，没有什么特别之处(如果你不知道你就console.log打印啊)
- JSX：就是用html的形式写 JS(js的扩展)
- 虚拟dom：非真实的dom，表示dom节点的对象

[代码仓库](https://github.com/slTrust/React-study)










