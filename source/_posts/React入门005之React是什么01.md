---
title: React入门005之React是什么01
date: 2019-01-15 21:01:00
tags: React入门
---

### Redux

### [一个不错的Redux中文文档](https://cn.redux.js.org/docs/basics/Reducers.html)

- 建议直接看

[示例](https://cn.redux.js.org/docs/introduction/Examples.html)



> ### 里面有个 Counter Vanilla示例

- Vanilla是什么 (英文意思香草)

what

> #### VanillaJS就代表 原生JS

因为有很多前端程序员上来就学框架，根本不懂底层原理只会用库或框架，于是很多人上手就学vue,此时让它用 原生他不会，于是有人为了嘲讽这些不会原生JS的人 发明了一个新的框架就叫 VanillaJS

- [VanillaJS官网](http://vanilla-js.com/) 需要翻墙 (而且你点击download里任何一个，下载大小都是0 bytes)

- [sf解释](https://segmentfault.com/a/1190000000355277?utm_source=tag-newest)

> Counter Vanilla就是 没有用任何的库(用redux结合原生JS)

- [示例地址](https://github.com/reduxjs/redux/blob/master/examples/counter-vanilla/index.html)

> #### 我们的需求 实现几个按钮点击触发+1/-1的功能

初始化如下

```
<!DOCTYPE html>
<html lang="en">
<body>
<script src="https://cdn.bootcss.com/redux/4.0.1/redux.min.js"></script>
<div id="app"></div>
<script>
function render(){
    var app = document.querySelector('#app');
    app.innerHTML = `
    <div>
        你点击了<span id="value">0</span>次
        <div>
            <button id="add1">+1</button>
            <button id="add2">+2</button>
            <button id="add1IfOdd">如果单数就+1</button>
            <button id="addAfter2Ses">2秒后+1</button>
        </div>
    </div>
    `;
}
render();
</script>
</body>
</html>
```

> 用redux怎么做呢？

根据[示例](https://github.com/reduxjs/redux/blob/master/examples/counter-vanilla/index.html)

1. 我们第一步要创建一个store 存这个0 
2. 这个store需要接受一个参数(更新store的函数)。
3. 这个参数是一个函数
    ```
    (接受两个参数)
    第一个是上一次 strore的状态
    第二个是 具体的操作

    var store = Redux.createStroe(函数xx)
    function 函数xx(之前的状态,操作){
        return 新的状态
    }
    ```
4. 这个函数是用来更新store的

- step1 dispatch 派发一个action
- step2 根据操作生成新的 state 触发一个事件
- step3 接受 事件重新 render

```
<!DOCTYPE html>
<html lang="en">
<body>
<script src="https://cdn.bootcss.com/redux/4.0.1/redux.min.js"></script>
<div id="app"></div>
<script>
function add1(){
    // step1 dispatch 派发一个action
    // 触发一个事件
    // 派发一个动作
    store.dispatch({type:'add'})
}

function render(store){
    var app = document.querySelector('#app');
    app.innerHTML = `
    <div>
        你点击了<span id="value">${store.getState()}</span>次
        <div>
            <button id="add1" onclick="add1()">+1</button>
            <button id="add2">+2</button>
            <button id="add1IfOdd">如果单数就+1</button>
            <button id="addAfter2Ses">2秒后+1</button>
        </div>
    </div>
    `;
}

function stateChanger(state,action){ 
    if(state === undefined){
        return 0;
    }else{
        if(action.type === 'add'){
            var newState = state + 1;
            return newState;
            // step2 根据操作生成新的 state 触发一个事件
        }else{
            return state;
        }
    }
}
//初始化 store
var store = Redux.createStore(stateChanger)

render(store);

//监听 store的变化 然后触发render
store.subscribe(()=>{

    // step3 接受 事件重新 render
    render(store) 

})
</script>
</body>
</html>
```

> store 和 state的关系

```
state    ------> store存储了 state
获取 state ------> store.getState()

store    ------> Redux.create(函数)
更新 state -----> 触发 action 也就是 dispatch({type:'操作'})
```

> #### 完善其他按钮的功能

- 新增payload使用
- 异步调用 action

```
<!DOCTYPE html>
<html lang="en">
<body>
<script src="https://cdn.bootcss.com/redux/4.0.1/redux.min.js"></script>
<div id="app"></div>
<script>
function add1(){
    store.dispatch({type:'add',payload:1})
}
function add2(){
    store.dispatch({type:'add',payload:2})
}

function add1IfOdd(){
    var oldState = store.getState()
    if(oldState % 2 ===1){
        store.dispatch({type:'add',payload:1})
    }
}

function addAfter2Ses(){
    setTimeout(() => {
        store.dispatch({type:'add',payload:1})
    }, 2000);
}

function render(store){
    var app = document.querySelector('#app');
    app.innerHTML = `
    <div>
        你点击了<span id="value">${store.getState()}</span>次
        <div>
            <button id="add1" onclick="add1()">+1</button>
            <button id="add2" onclick="add2()">+2</button>
            <button id="add1IfOdd" onclick="add1IfOdd()">如果单数就+1</button>
            <button id="addAfter2Ses" onclick="addAfter2Ses()">2秒后+1</button>
        </div>
    </div>
    `;
}

function stateChanger(state,action){ 
    if(state === undefined){
        return 0;
    }else{
        console.log('add')
        if(action.type === 'add'){
            var newState = state + action.payload;
            return newState;
            // step2 根据操作生成新的 state 触发一个事件
        }else{
            return state;
        }
    }
}
//初始化 store
var store = Redux.createStore(stateChanger)

render(store);

//监听 store的变化 然后触发render
store.subscribe(()=>{
    // step3 接受 事件重新 render
    render(store) 

})
</script>
</body>
</html>
```

- ### 一个坑，就是异步更新 store的问题

```
// 新手经常犯的错误 ,把异步逻辑写在 
function asyncAdd2(){
    store.dispatch({type:'add2',payload:2})
}


function stateChanger(state,action){ 
    if(state === undefined){
        return 0;
    }else{
        if(action.type === 'add'){
            var newState = state + action.payload;
            return newState;
            // step2 根据操作生成新的 state 触发一个事件
        }else if(action.type ==='add2'){
             setTimeout(() => {
                var newState = state + action.payload;
                return newState;
            }, 2000);

            // 此时 setTimeout里函数的return是 箭头函数的返回值
            /*
            而这个 
            else if(action.type ==='add2'){
                //没写 return
                // 相当于 return undefined
            }
            */
        }else{
            return state;
        }
    }
}


```





