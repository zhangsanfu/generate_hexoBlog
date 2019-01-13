---
title: React入门003之React通信
date: 2019-01-13 10:50:28
tags: React入门
---

### 生趣例子——龟兔赛跑

- 请使用 jsbin
- 拷贝对应代码到对应区域
- JavaScript 选择 (React)Javascript

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
    .header{
    display:flex;
    justify-content:center;
    }

    .track{
    border:1px solid black;
    }
    </style>
</head>
<body>
<div id="root"></div>
<script>

function App(){
    return (
      <div>
        <div class="header">
            <Time1/>
            <Judge/>
            <Time2/>
        </div>
        <Track1/>
        <Track2/>
      </div>
    )
}

// 计时器
function Time1(){
    return (
        <div>
            <h2>🐰用时</h2>
            <div>0</div>
        </div>
    )
}

// 计时器
function Time2(){
    return (
        <div>
            <h2>🐢用时</h2>
            <div>0</div>
        </div>
    )
}

// 裁判
function Judge(){
    return (
        <div>裁判</div>
    )
}
// 跑道
function Track1(){
    return (
        <div>
            <div>🐰</div>
            <div class="track"></div>
        </div>
    )
}
// 跑道
function Track2(){
    return (
        <div>
            <div>🐢</div>
            <div class="track"></div>
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

### 需求2：如何让兔子动？

- css就可以做到

```
只要让 Track1里的  
<div style="transform: translateX(12%);">🐰</div>
```

- 新问题:让兔子动就要改变 这个div的style===》如何改变div的style

1. 你不能在内部改变函数组件的东西
2. 你需要把 Track1变成一个 class组件

```
class Track1 extends React.Component{
    constructor(){
        this.state = {
            progress:0
        }
    }
    render(){ 
        return(
            <div>
                <div style="transfrom:translateX(0%);">🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
```

> 卡壳了：React如何设置 style

google搜 react style

得到一个结果 [ Inline Styles | React](https://zhenyong.github.io/react/tips/inline-styles.html)点进去

给了个例子

```
var divStyle = {
  color: 'white',
  backgroundImage: 'url(' + imgUrl + ')',
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
};

ReactDOM.render(<div style={divStyle}>Hello World!</div>, mountNode);
```

> 继续改写我们的 Track

```
class Track1 extends React.Component{
    constructor(){
        super() // 必须调用 super 否则报错
        this.state = {
            style:{
                transform:`translateX(20%)`
            }
        }
    }
    render(){ 
        return(
            <div>
                <div style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
```

> 定时器让 兔子 动

```
class Track1 extends React.Component{
    constructor(){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        setInterval(()=>{
            n += 1
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
        },100)
    }
    render(){ 
        return(
            <div>
                <div style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
```

> 兔子速度快一点，乌龟跑的慢一点（改写两个track）

```
// 跑道
class Track1 extends React.Component{
    constructor(){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        setInterval(()=>{
            n += 10
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
// 跑道
class Track2 extends React.Component{
    constructor(){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        setInterval(()=>{
            n += 1
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐢</div>
                <div class="track"></div>
            </div>
        )
    }
}
```

为什么一跳一跳的？

```
// 线性匀速
.player{
    transition:all 1s linear;
}
```

> 跑到终点就停下

```
class Track1 extends React.Component{
    constructor(){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 10
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
```

### 重点环节 (通信)

>  跑到终点告诉裁判我停了

- 父组件传递监听的事件 success1/success2
- props传递属性 success
- 事件完成后 调用 props.success()

```

function App(){
    let success1 = ()=>{console.log('兔子跑完了')}
    let success2 = ()=>{console.log('乌龟跑完了')}
    return (
      <div>
        <div class="header">
            <Time1/>
            <Judge/>
            <Time2/>
        </div>
        <Track1 success={success1}/>
        <Track2 success={success2}/>
      </div>
    )
}

// 计时器
function Time1(){
    return (
        <div>
            <h2>🐰用时</h2>
            <div>0</div>
        </div>
    )
}

// 计时器
function Time2(){
    return (
        <div>
            <h2>🐢用时</h2>
            <div>0</div>
        </div>
    )
}

// 裁判
function Judge(){
    return (
        <div>裁判</div>
    )
}
// 跑道
class Track1 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 10
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
// 跑道
class Track2 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 5
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐢</div>
                <div class="track"></div>
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

> 一个小bug就是 兔子没跑到终点就打印了 跑完了 因为 css里的 线性动画( 移除 player样式即可)

> 跑到终点所用时间

```
function App(){
    let t0 = new Date();

    let success1 = ()=>{
        console.log('兔子跑完了')
        console.log(new Date() - t0)
    }
    let success2 = ()=>{
        console.log('乌龟跑完了')
        console.log(new Date() - t0)
    }
    
    // ...
    // ...
}
```

> ### 将结束时间更新到 Time1/Time2上

```
function App(){
    return (
      <div>
        <div class="header">
            <Time1 result={}/>
            <Judge/>
            <Time2/>
        </div>
        <Track1 success={success1}/>
        <Track2 success={success2}/>
      </div>
    )
}
```

- 如何单独把结果render 到Timer1呢？
- 不好意思函数组件是做不到的

> 用 class组件改写 app

```
class App extends React.Component{
    constructor(){
        super()
        this.state = {
            result1:0,
            result2:0
        }
        this.t0 = new Date()
    }
    success1(){
        console.log('兔子跑完了')
        console.log(new Date() - this.t0)
    }
    success2(){
        console.log('乌龟跑完了')
        console.log(new Date() - this.t0)
    }
    render(){
        return (
            <div>
                <div class="header">
                    <Time1 result={this.state.result1}/>
                    <Judge/>
                    <Time2 result={this.state.result1}/>
                </div>
                <Track1 success={this.success1.bind(this)}/>
                <Track2 success={this.success2.bind(this)}/>
            </div>
        )
    }
}
```

> 完整版

```

class App extends React.Component{
    constructor(){
        super()
        this.state = {
            result1:0,
            result2:0
        }
        this.t0 = new Date()
    }
    success1(){
        let t1 = new Date() - this.t0;
        console.log('兔子跑完了',t1)
        this.setState({
            result1:t1
        })
    }
    success2(){
        let t2 = new Date() - this.t0;
        console.log('乌龟跑完了',t2)
        this.setState({
            result2:t2
        })
    }
    render(){
        return (
            <div>
                <div class="header">
                    <Time1 result={this.state.result1}/>
                    <Judge/>
                    <Time2 result={this.state.result2}/>
                </div>
                <Track1 success={this.success1.bind(this)}/>
                <Track2 success={this.success2.bind(this)}/>
            </div>
        )
    }

}

// 计时器
function Time1(props){
    return (
        <div>
            <h2>🐰用时</h2>
            <div>{props.result}</div>
        </div>
    )
}

// 计时器
function Time2(props){
    return (
        <div>
            <h2>🐢用时</h2>
            <div>{props.result}</div>
        </div>
    )
}

// 裁判
function Judge(){
    return (
        <div>裁判</div>
    )
}
// 跑道
class Track1 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 25
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
// 跑道
class Track2 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 15
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐢</div>
                <div class="track"></div>
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

### 面试题

> 请问 React的父子组件如何通信

```
父组件通过props传递一个函数给子元素 
子元素在恰当的时候调用这个函数
```

### 需求变更(添加操场)

- App里是 操场 Playground
- 操场Playground里有跑道Track

> 咋办呢？ 很简单 把success传递给 操场
> 然后在操场里通过 props接受 再次传递给 Track

代码如下

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
    *{margin:0;padding:0;box-sizing: border-box;}
    .header{
        display:flex;
        justify-content:center;
    }

    .track{
        border:1px solid black;
    }
    .player{
        transition:all 1s linear;
    }
    .playground{
        border:1px solid black;
        background: green;
    }
    </style>
</head>
<body>
<div id="root"></div>
<script>

class App extends React.Component{
    constructor(){
        super()
        this.state = {
            result1:0,
            result2:0
        }
        this.t0 = new Date()
    }
    success1(){
        let t1 = new Date() - this.t0;
        console.log('兔子跑完了',t1)
        this.setState({
            result1:t1
        })
    }
    success2(){
        let t2 = new Date() - this.t0;
        console.log('乌龟跑完了',t2)
        this.setState({
            result2:t2
        })
    }
    render(){
        return (
            <div>
                <div class="header">
                    <Time1 result={this.state.result1}/>
                    <Judge/>
                    <Time2 result={this.state.result2}/>
                </div>
                <Playground success1={this.success1.bind(this)}
                            success2={this.success2.bind(this)}
                />
            </div>
        )
    }

}

function Playground(props){
    let success1 = props.success1
    let success2 = props.success2
    return (
        <div class="playground">
            <Track1 success={success1}/>
            <Track2 success={success2}/>
        </div>
    )
}

// 计时器
function Time1(props){
    return (
        <div>
            <h2>🐰用时</h2>
            <div>{props.result}</div>
        </div>
    )
}

// 计时器
function Time2(props){
    return (
        <div>
            <h2>🐢用时</h2>
            <div>{props.result}</div>
        </div>
    )
}

// 裁判
function Judge(){
    return (
        <div>裁判</div>
    )
}
// 跑道
class Track1 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 25
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐰</div>
                <div class="track"></div>
            </div>
        )
    }
}
// 跑道
class Track2 extends React.Component{
    constructor(props){
        super() // 必须调用 super 否则报错
        let n = 0;
        this.state = {
            style:{
                transform:`translateX(${n}%)`
            }
        };
        let timerId = setInterval(()=>{
            n += 15
            this.setState({
                style:{transform:`translateX(${n}%)`}
            })
            if(n>=100){
                window.clearInterval(timerId)
                //通知 我跑完了
                this.props.success()
            }
        },1000)
    }
    render(){ 
        return(
            <div>
                <div class="player" style={this.state.style}>🐢</div>
                <div class="track"></div>
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

### 面试肯定问爷孙通信是咋实现的

```
就像上面一样 Playground 从 App哪里把属性props传递下来(继承下来) 传递给 Track
```

> 如果是曾爷爷怎么办

当然是顺着这个家族在多传递一次了


> #### 思考？如果是 兄弟之间如何通信呢？

下一篇我们来解决

[代码仓库](https://github.com/slTrust/React-study)