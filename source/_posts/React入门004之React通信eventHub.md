---
title: React入门004之React通信eventHub
date: 2019-01-13 17:25:41
tags: React入门
---

### 组件通信的回顾

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
</head>
<body>
<div id="root"></div>
<script>

class App extends React.Component{
    constructor(){
        super()
        this.state = {
            msg :'你好！'
        }
    }
    changeMsg(){
        this.setState({
            msg:'你也好！'
        })
    }
    render(){
        return (
            <div>
                <div>
                    <Box msg={this.state.msg} 
                        fn={this.changeMsg.bind(this)}
                    />
                </div>
            </div>
        )
    }
}



// 计时器
function Box(props){
    return (
        <div>
            <h2>得到父组件的消息为{props.msg}</h2>
            <div onClick={props.fn}>回复</div>
        </div>
    )
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

### 一个家族的通信模型

- 打开jsbin
- 添加react支持的库文件
- Javascript选择 React(JSX)

如图
![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study004-01.png)


```
<!DOCTYPE html>
<html lang="en">
<head>
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

class App extends React.Component{
    constructor(){
        super()
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather/>
                <SecondFather/>
            </div>
        )
    }
}

function FirstFather(props){
    return (
        <div className="papa">
            <div>大爸</div>
            <Son1/>
            <Son2/>
        </div>
    )
}

function SecondFather(props){
    return (
        <div className="papa">
            <div>二爸</div>
            <Son3/>
            <Son4/>
        </div>
    )
}

function Son1(){
    return (
        <div className="son">
            儿子1
        </div>
    )
}
function Son2(){
    return (
        <div className="son">
            儿子2
        </div>
    )
}

function Son3(){
    return (
        <div className="son">
            儿子3
        </div>
    )
}

function Son4(){
    return (
        <div className="son">
            儿子4
        </div>
    )
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

> 一个家族里爷爷留给后代的所有财产 100000元，后代们一起使用

1. 二儿子用100 其他人怎么同步余额
2. 把所有金额通知给所有人

```
var money = {
    amount : 100000
}

class App extends React.Component{
    constructor(){
        super()
       
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather/>
                <SecondFather/>
            </div>
        )
    }
}

class FirstFather extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="papa">
                <div>大爸 {this.state.money.amount}</div>
                <Son1/>
                <Son2/>
            </div>
        )
    }
}

class SecondFather extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="papa">
                <div>二爸 {this.state.money.amount}</div>
                <Son3/>
            <Son4/>
            </div>
        )
    }
}

class Son1 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="son">
                儿子1 {this.state.money.amount}
            </div>
        )
    }
}

class Son2 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    x(){
        money.amount -= 100
        this.setState({
            money:money
        })
    }
    render(){
        return (
            <div className="son">
                儿子2 {this.state.money.amount}
                <button onClick={()=>this.x()}>消费</button>
            </div>
        )
    }
}

class Son3 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="son">
                儿子3 {this.state.money.amount}
            </div>
        )
    }
}

class Son4 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="son">
                儿子4 {this.state.money.amount}
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
```

> 现在点击消费按钮，只有二儿子的数据更新了

- 虽然用的是同一个 money 对象
- 但是这些组件你都没有去调用它的setState方法，所以它们不知道你消费的动作，所以一直保留旧的金额

> 一种假设 假如 大儿子 二儿子 都是败家子 

1. 二儿子去酒店把100000  花完了
2. 大儿子不知道钱已经没了 还觉得有10万块，去夜店消费 ，消费后发现 没钱了——被暴打了一顿
3. 这样就很不好

> 如果用上一篇的办法就会导致，用如下迂回的方式

1. 二儿子消费了100后,通知它大爸
2. 大爸通知大儿子 通知二爸 
3. 二爸通知三儿子 二爸通知四儿子

- 这样一看就很垃圾

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study004-02.png)

> #### 有木有一种方案让任意两个组件之间都可以通信

- 雇一个管家 让他专门管理财务
- 此时二儿子通知管家我要花钱  如果是小金额 管家直接给了
- 如果是大金额 管家就通知 大爸 这钱能不能给二儿子 大爸说可以  就把钱给 二儿子
- 这就是我们现实生活中所有人共用一个数据 矛盾的情况

> 雇一个人专门管这件事

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study004-03.png)

### 发布订阅模式(EventHub)

代码实现

```
//订阅的事件
var eventMap = {
    // 蒙牛:['张三','李四','王五'],
    // 光明:['熊大','光头强']
};
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

// 订阅——事件
eventHub.on('我要用钱',function fn1(data){
    console.log(data)
})

// 发布——事件
eventHub.trigger('我要用钱',100)
```

> 代码修改如下 二儿子花钱了 通知 三儿子

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

class App extends React.Component{
    constructor(){
        super()
       
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather/>
                <SecondFather/>
            </div>
        )
    }
}

class FirstFather extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="papa">
                <div>大爸 {this.state.money.amount}</div>
                <Son1/>
                <Son2/>
            </div>
        )
    }
}

class SecondFather extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="papa">
                <div>二爸 {this.state.money.amount}</div>
                <Son3/>
            <Son4/>
            </div>
        )
    }
}

class Son1 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="son">
                儿子1 {this.state.money.amount}
            </div>
        )
    }
}

class Son2 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    x(){
        money.amount -= 100
        eventHub.trigger('我要花钱',100)
        this.setState({
            money:money
        })
    }
    render(){
        return (
            <div className="son">
                儿子2 {this.state.money.amount}
                <button onClick={()=>this.x()}>消费</button>
            </div>
        )
    }
}

class Son3 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
        eventHub.on('我要花钱',(data)=>{
            this.setState({
                money:money
            })
        })
    }
    render(){
        return (
            <div className="son">
                儿子3 {this.state.money.amount}
            </div>
        )
    }
}

class Son4 extends React.Component{
    constructor(){
        super()
        this.state = {
            money : money
        }
    }
    render(){
        return (
            <div className="son">
                儿子4 {this.state.money.amount}
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
```

> #### 新问题：如果 大爸 二爸 二儿子的兄弟 都想知道呢？

- 总不能每个组件都 监听这个 事件吧，这样就太麻烦了

> #### 之所以会这样是因为：你们这些人自己随便花钱，二儿子花了钱谁也不告诉，直接把钱花了，然后在发信息

- 先斩后奏(不行)
- 该如何处理呢？

> #### 这个钱你们谁也不准改

1. 花钱必须通知管家，(你不能把钱花了在告诉管家，管家帮你擦屁股)
2. 所有组件不能修改money
3. 不能修改money我咋花钱呢？ 花钱可以——发消息
4. 移除所有组件里 money的引用 也就是不要用 state
5. 改用 props 放在 顶层的app里

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

class App extends React.Component{
    constructor(){
        super()
        this.state = {
            money:money
        }
       
    }
    render(){
        return (
            <div className="root">
                <div>app</div>
                <FirstFather money={this.state.money}/>
                <SecondFather money={this.state.money}/>
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
        eventHub.trigger('我想花钱',100)
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

- 点击消费按钮时候——如何触发的更新呢？

```
chrome里查看dom解构，只有文字改变了元素还在

这就是 React的 NB之处——只更新需要更新的地方 DOM diff
```


> #### 这就是——单向数据流

- 数据放在顶层
- 通过props一层层的传递下去

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study004-05.png)

以前我们只能这样：有上有下 (就不是单向数据流)

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study004-04.png)




