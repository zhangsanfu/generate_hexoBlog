---
title: React入门004之React通信Redux代替eventHub.html
date: 2019-01-14 22:36:38
tags: React入门
---

### eventHub模式的理解

```
var money = {
    amount : 100000
}

var eventMap = {};
var eventHub = {
    trigger(eventName,data){
        let fnList = eventMap[eventName];
        if(!fnList) return 
        for(var i=0;i<fnList.length;i++){
            fnList[i](data)
        }
    },
    on(eventName,fn){
        if(!eventMap[eventName]){
            eventMap[eventName] = []
        }
        eventMap[eventName].push(fn)
    }
}

var 管家 = {
    init(){
        eventHub.on('我想花钱',(data)=>{
            money.amount -= data
            // 此时要调用render才能实现 数据的同步
            render();
        })
    }
}

管家.init()
```

> 特点

1. 所有的数据放在顶层 上一篇我们的money放在了 app中 Redux 推荐把所有数据放归置在一个地方，然后把数据传递给最顶层组件(App)
    
> 此时有一个问题发生了,假如我有多个数据呢？ 你就要一个一个属性的传下去

```
var money = {
    amount : 100000
}
var user = {
    id:321312,
    nickName:'土豪'
}

// 此时需要在app里
class App extends React.Component{
    constructor(){
        super()
        this.state = {
            money:money,
            user:user
        }
       
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather money={this.state.money}
                user={this.state.user}
                />
            </div>
        )
    }
}

```

### eventHub 深入

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
    <script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>
    <style>
        .root{
            display: flex;
            justify-content: center;
            align-items: center;
            padding:10px;
            border:1px solid black;
        }
        .papa{
            border:1px solid grey;
            padding:10px;
        }
        .son{
            border:1px solid grey;
            padding:10px;
        }
    </style>
</head>
<body>
<div id="root"></div>
<script>
var user = {
    id:3213123,
    nickName:'土豪'
}
var money = {
    amount : 100000
}

var store = {
    money:money,
    user:user
}

var eventMap = {};
var eventHub = {
    trigger(eventName,data){
        let fnList = eventMap[eventName];
        if(!fnList) return 
        for(var i=0;i<fnList.length;i++){
            fnList[i](data)
        }
    },
    on(eventName,fn){
        if(!eventMap[eventName]){
            eventMap[eventName] = []
        }
        eventMap[eventName].push(fn)
    }
}

var 管家 = {
    init(){
        eventHub.on('我想花钱',(data)=>{  //订阅 subscribe
            store.money.amount -= data // reducer 对数据的变动
            // 此时要调用render才能实现 数据的同步
            render();
        })
    }
}

管家.init()

class App extends React.Component{
    constructor(){
        super()
        this.state = {
            store:store
        }
       
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather money={this.state.store.money}/>
                <SecondFather money={this.state.store.money}/>
            </div>
        )
    }
}



class FirstFather extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="papa">
                <div>大爸 {this.props.money.amount}</div>
                <Son1 money={this.props.money}/>
                <Son2 money={this.props.money}/>
            </div>
        )
    }
}

class SecondFather extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="papa">
                <div>二爸 {this.props.money.amount}</div>
                <Son3 money={this.props.money}/>
                <Son4 money={this.props.money}/>
            </div>
        )
    }
}

class Son1 extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="son">
                儿子1 {this.props.money.amount}
            </div>
        )
    }
}


class Son2 extends React.Component{
    constructor(){
        super()
    }
    x(){
        // active       
        eventHub.trigger('我想花钱' /* action type */,100) //100叫做 payload
    }
    render(){
        return (
            <div className="son">
                儿子2 {this.props.money.amount}
                <button onClick={()=>this.x()}>消费</button>
            </div>
        )
    }
}


class Son3 extends React.Component{
    constructor(){
        super()
        eventHub.on('我要花钱',(data)=>{
            this.setState({
                money:money
            })
        })
    }
    render(){
        return (
            <div className="son">
                儿子3 {this.props.money.amount}
            </div>
        )
    }
}


class Son4 extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="son">
                儿子4 {this.props.money.amount}
            </div>
        )
    }
}

function render(){
    ReactDOM.render(
        <App/>,
        document.querySelector('#root')
    )
}

render();
</script>
</body>
</html>
```

- store 就是 集中管理数据的地方
- action 就是事件 
- 我要花钱就是  action type
- 100金额 就是 payload 载荷
- reducer 对数据的变动
- subscribe 订阅

```
var user = {
    id:3213123,
    nickName:'土豪'
}
var money = {
    amount : 100000
}

var store = {
    money:money,
    user:user
}


eventHub.trigger('我想花钱' /* action type */,100) //100叫做 payload

eventHub.on('我想花钱',(data)=>{  //订阅 subscribe
    store.money.amount -= data // reducer 对数据的变动
    render();
})
```

### 用 Redux代替 eventHub

- bootcdn  里 搜索 redux 并引入

```
<script src="https://cdn.bootcss.com/redux/4.0.0/redux.min.js"></script>
```

- 由于我们是 JS引入 和 webpack或许有差异
- 此时我们不知道咋用，咋办？ crm

1. 谷歌搜 Redux  科学上网的重要性
2. https://cn.redux.js.org/ (翻墙可看)
3. 找到 例子   示例 --> https://cn.redux.js.org/docs/introduction/Examples.html
4. Counter 例子 https://github.com/reduxjs/redux/tree/master/examples/counter
5.  crm
6. 抄它怎么调的redux https://github.com/reduxjs/redux/blob/master/examples/counter/src/index.js

    ```
    import React from 'react'
    import ReactDOM from 'react-dom'
    import { createStore } from 'redux'
    import Counter from './components/Counter'
    import counter from './reducers'

    const store = createStore(counter)
    const rootEl = document.getElementById('root')

    const render = () => ReactDOM.render(
    <Counter
        value={store.getState()}
        onIncrement={() => store.dispatch({ type: 'INCREMENT' })}
        onDecrement={() => store.dispatch({ type: 'DECREMENT' })}
    />,
    rootEl
    )

    render()
    store.subscribe(render)
    ```



```
//别忘记引入redux.js
// 通过抄别人的  我们造出了 自己的store
let createStore = Redux.createStore;
let reducers = (state = 0 ,action)=>{
    state = state ||{
        money:{ amoune:100000 }
    }
    switch(action.type){
        case 'a':
            return state + 1
        case 'b':
            return state - 1
        default:
            return state
    }
}

const store = createStore(reducers);
console.log(store.getState())
```

> 此时之前的 eventHub 和 管家都不需要了

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
    <script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>
    <script src="https://cdn.bootcss.com/redux/4.0.0/redux.min.js"></script>
    <style>
        .root{
            display: flex;
            justify-content: center;
            align-items: center;
            padding:10px;
            border:1px solid black;
        }
        .papa{
            border:1px solid grey;
            padding:10px;
        }
        .son{
            border:1px solid grey;
            padding:10px;
        }
    </style>
</head>
<body>
<div id="root"></div>
<script>
let createStore = Redux.createStore;

//管家 ——一个函数 他会得到（之前的state，花钱的动作）
let reducers = (state = 0 ,action)=>{
    //初始化
    state = state ||{
        money:{ amount:100000 }
    }
    switch(action.type){
        case '我想花钱':
            return {
                money:{
                    amount:state.money.amount - action.payload
                }
            }
        default:
            return state
    }
}

const store = createStore(reducers);
console.log(store.getState())

class App extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather money={this.props.store.money}/>
                <SecondFather money={this.props.store.money}/>
            </div>
        )
    }
}



class FirstFather extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="papa">
                <div>大爸 {this.props.money.amount}</div>
                <Son1 money={this.props.money}/>
                <Son2 money={this.props.money}/>
            </div>
        )
    }
}

class SecondFather extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="papa">
                <div>二爸 {this.props.money.amount}</div>
                <Son3 money={this.props.money}/>
                <Son4 money={this.props.money}/>
            </div>
        )
    }
}

class Son1 extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="son">
                儿子1 {this.props.money.amount}
            </div>
        )
    }
}


class Son2 extends React.Component{
    constructor(){
        super()
    }
    x(){
        // active       
        // eventHub.trigger('我想花钱' /* action type */,100) //100叫做 payload
        store.dispatch({type:'我想花钱',payload:100})
    }
    render(){
        return (
            <div className="son">
                儿子2 {this.props.money.amount}
                <button onClick={()=>this.x()}>消费</button>
            </div>
        )
    }
}


class Son3 extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="son">
                儿子3 {this.props.money.amount}
            </div>
        )
    }
}


class Son4 extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="son">
                儿子4 {this.props.money.amount}
            </div>
        )
    }
}

function render(){
    ReactDOM.render(
        <App store={store.getState()}/>,
        document.querySelector('#root')
    )
}

render()
// 订阅数据的改变同步更新
store.subscribe(render)
</script>
</body>
</html>
```

> 是不是觉得Redux很啰嗦

- eventHub能看懂
- 用 redux反而很恶心

> #### Redux为啥这样做

- 所有的框架都有一个使命——防呆(怕你的队友是个猪队友)
- 所以Redux最大的功能就是防呆

> eventHub的问题

- 事件名可能任意的，分散在各处，而你用redux强制要求你集中的在 switch里
    ```
    let reducers = (state = 0 ,action)=>{
        //初始化
        state = state ||{
            money:{ amount:100000 }
        }
        switch(action.type){
            case '我想花钱':
                return {
                    money:{
                        amount:state.money.amount - action.payload
                    }
                }
            default:
                return state
        }
    }
    ```

- redux的约束 你必须每个事件给我一个事件名和 payload 并且列在一起
- 你只能用 props的方式 使用 store={store.getState()}

    ```
    //getState()只读的方式告诉你  不要修改
    //因为通过 props能防猪队友 

    // 猪队友
    // 通过引用修改数据
    this.props.money.amount -= 200
    // 即使用redux 依然也是可以通过引用改的
    这是 JS的 bug 
    ```
- redux可以让不懂英语的前端 走远一点
- 比如 payload 实际就是个 data 
