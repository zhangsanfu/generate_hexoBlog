---
title: React入门002之React组件
date: 2019-01-11 20:54:17
tags: React入门
---

### 函数组件

上一篇文章我们实现了 JSX语法写 html

```
function render(){
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

> ### 假如我们写淘宝页面呢？

- 1000行铁定不够的
- 总不能在一个render里 写1000行以上这样的结构吧

```
function render(){
    ReactDOM.render(
        <div>
            <div class="container">
                <span class="red"> { number } </span>
                <button onClick= { onClickButton } >+</button>
                <button onClick= { onClickButton2 } >-</button>
            </div>
            <div class="container">
                <span class="red"> { number } </span>
                <button onClick= { onClickButton } >+</button>
                <button onClick= { onClickButton2 } >-</button>
            </div>
            <div class="container">
                <span class="red"> { number } </span>
                <button onClick= { onClickButton } >+</button>
                <button onClick= { onClickButton2 } >-</button>
            </div>
            .... 写xxx行
            <div class="container">
                <span class="red"> { number } </span>
                <button onClick= { onClickButton } >+</button>
                <button onClick= { onClickButton2 } >-</button>
            </div>
        </div>,
        document.querySelector('#root')
    )
}
```

> #### 于是React作者就在想，如何能避免所有代码写在一坨呢？

- 最简单的方法就是用一个——函数 来把代码包裹起来

> ### 看看我们的代码(请使用jsbin)

```
# 使用这个
https://jsbin.com/

# 引入
<script src="https://cdn.bootcss.com/react/16.7.0-alpha.2/umd/react.development.js"></script>  
<script src="https://cdn.bootcss.com/react-dom/16.7.0-alpha.2/umd/react-dom.production.min.js"></script>

# javascript选择 (JSX)React 否则报错
```

> ### 一个非常大的坑 return 后面不要直接换行—— 那样返回的是 undefined

```
// 返回的是 undefined
// 返回的是 undefined
// 返回的是 undefined
function App(){
    return 
        <div>
            <span>{number}</span>
        </div>
}

```

> ### 我就想换行咋办,加()

```
function App(){
    return (
        <div>
            <span>{number}</span>
        </div>
    )
}
```

> ### 看代码

```
let number = 0 ;
let add = ()=>{
  number += 1;
  render();
}
let minus = ()=>{
  number -= 1;
  render();
}
render()

function App(){
  return (
    <div class="container">
        <span class="red"> { number } </span>
        <button onClick= { add } >+</button>
        <button onClick= { minus } >-</button>
    </div>
  )
}

function render(){
    ReactDOM.render(
        React.createElement(App),
        document.querySelector('#root')
  )
}

// ------华丽的分割线---------------------------------------------------
// render里 React.createElement(App), 是不是很丑
// 由于我们用的是 JSX
// 你可以这样
function render(){
    ReactDOM.render(
        <App/>,
        document.querySelector('#root')
    )
}

// 或者

function render(){
    ReactDOM.render(
        <App></App>,
        document.querySelector('#root')
    )
}

```

- JSX语法

```
<App></App>  <==等价于==>  React.createElement(App)

标签还可以再次简写为 ，因为它不是 html语法 它是 xml语法(标签里没有任何东西，可以自闭合)
<App/> 
```

> 优化一下

```
let number = 0 ;
let add = ()=>{
  number += 1;
  render();
}
let minus = ()=>{
  number -= 1;
  render();
}
render()

function App(){
  return (
    <div>
        <Box1/>
        <Box2/>
    </div>
  )
}

function Box1(){
  return (
    <div class="container">
    <span class="red"> { number } </span>
    <button onClick= { add } >+</button>
    <button onClick= { minus } >-</button>
    </div>
  )
}

function Box2(){
  return (
    <div class="container">
    <span class="red"> { number } </span>
    <button onClick= { add } >+</button>
    <button onClick= { minus } >-</button>
    </div>
  )
}

function render(){
  ReactDOM.render(
    <App/>,
    document.querySelector('#root')
  )
}
```

> ### 以上就是组件的萌芽想法

### 如何给 标签传递参数呢？

```
let number = 0 ;
let add = ()=>{
    number += 1;
    render();
}
let minus = ()=>{
    number -= 1;
    render();
}
render()

function App(){
    return (
        <div>
            <Box1 name="加一"/>
            <Box2 name="加二"/>
        </div>
    )
}

function Box1(obj){
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number } </span>
            <button onClick= { add } >+</button>
            <button onClick= { minus } >-</button>
        </div>
    )
}

function Box2(obj){
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number } </span>
            <button onClick= { add } >+</button>
            <button onClick= { minus } >-</button>
        </div>
    )
}

```

- react的作者非常聪明，他把属性理解为对象的一个key/value

> React的创举就是

- 虚拟dom
- 标签就是函数，函数就是对象，标签的属性就是函数的参数

### 上述代码的Box1和Box2有一个bug

```
点击 Box1的 "+" Box2的数字也增加了。

因为点击 Box1的 时候 触发了 add 导致 number++ 然后 render() 

在render()的时候重新渲染了，而且 Box1 和 Box2使用的是同一个 number
```

> 此时你肯定想，既然能给组件传递参数，那我们传递一个number 这样 Box1和Box2就互不影响了

- 答案是不行，因为React规定了一个事——不允许修改别人传递给你的参数
- 我就要改，会给你一个警告。
- 永远不要修改别人给你的属性，你修改了，不会有什么后果，但是后果自负。肯定会有bug,可能你现在不会觉得有bug，但是过了几个月一定会有bug

> 最朴素的想法

```
let number1 = 0 ;
let add1 = ()=>{
    number1 += 1;
    render();
}
let minus1 = ()=>{
    number1 -= 1;
    render();
}

let number2 = 0 ;
let add2 = ()=>{
    number2 += 1;
    render();
}
let minus2 = ()=>{
    number2 -= 1;
    render();
}
render()

function App(){
    return (
        <div>
            <Box1 name="加一"/>
            <Box2 name="加二"/>
        </div>
    )
}

function Box1(obj){
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number1 } </span>
            <button onClick= { add1 } >+</button>
            <button onClick= { minus1 } >-</button>
        </div>
    )
}

function Box2(obj){
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number2 } </span>
            <button onClick= { add2 } >+</button>
            <button onClick= { minus2 } >-</button>
        </div>
    )
}

function render(){
    ReactDOM.render(
        <App/>,
        document.querySelector('#root')
    )
}
```

> #### 虽然不相互影响了

- 但是多出了很多无用变量
- 如果是100个组件呢？ 声明100个这样的岂不是疯了

> 卡壳了

- 我不能修改别人给我的属性
- 如果有100个组件就要100个这样的重复声明，一看页面要懵了

> 又想到一个好办法，将 number属性内置

- 

```
render()

function App(){
    return (
        <div>
            <Box1 name="加一"/>
            <Box2 name="加二"/>
        </div>
    )
}

function Box1(obj){
    let number1 = 0 ;
    console.log('把number变成 0',number1)
    let add1 = ()=>{
        number1 += 1;
        console.log('把number变成 1',number1)
        render();
    }
    let minus1 = ()=>{
        number1 -= 1;
        render();
    }
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number1 } </span>
            <button onClick= { add1 } >+</button>
            <button onClick= { minus1 } >-</button>
        </div>
    )
}

function Box2(obj){
    let number2 = 0 ;
    let add2 = ()=>{
        number2 += 1;
        render();
    }
    let minus2 = ()=>{
        number2 -= 1;
        render();
    }
    return (
        <div class="container">
            我的name是{obj.name}
            <span class="red"> { number2 } </span>
            <button onClick= { add2 } >+</button>
            <button onClick= { minus2 } >-</button>
        </div>
    )
}

function render(){
    ReactDOM.render(
        <App/>,
        document.querySelector('#root')
    )
}

/*
思路是没错的
错就错在了 render 
*/

```

> 此时 点击 add 没反应了

- 思路很好将变量内置
- 错就错在 render了

```
点击 add 触发 number+=1 
然后 调用 render()
render 执行会调用 Box1
Box1里  let number = 0 
所以你怎么点击 都是0

--------------------------------
虽然思路非常好，但是现实把你打败了，你做不到，因为函数的功能过于单一了
此时再次陷入窘境
```

> 我们为什么出错,因为这个render，一render就render整个页面

> 如果不render整个页面，只render自己呢？

- 肯定不能用函数来搞组件了
- 不能render整个app

> react作者想到，什么比函数高级呢？——类

### class组件

语法规定

- 继承React.Component
- 必须实现 render方法

```
class App2 extends React.Component{
    render(){
        return (
            <div>app2</div>
        )
    }
}

function render(){
    ReactDOM.render(
        <App2/>,
        document.querySelector('#root')
    )
}

render();
```

> #### 传递参数呢？

- 注意！ 我们管接受到的参数叫 props
- 注意！ 我们管接受到的参数叫 props
- 注意！ 我们管接受到的参数叫 props

```
// 之前传递参数
function App(props){
    return (
        <div>
            app {props.name}
        </div>
    )
}

// 现在使用参数
// 这个 props 是由父类React.Component构造的 你不继承就没有
class App2 extends React.Component{
    render(){ // 局部 render
        return (
            <div>app2 {this.props.name} {this.props.age}</div>
        )
    }
}

function render(){
    ReactDOM.render(
        <App2 name="hjx" age="18"/>,
        document.querySelector('#root')
    )
}

render();
// 注意在 html里 没有 数字 只有字符串
```

> #### 组件的局部变量呢？可以了这次

- 文档规定局部变量必须在 constructor 里设置
- this.state = {k:v,k2:v2}

```
// 规定组件的参数叫做 props 
class App2 extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            number:0
        }
    }
    add(){

        /*
        //报错
        this.state.number +=1
        // 而且这里的this 是 undefined
        // 因为react 调用 onClick的时候是这样调用的
        onClick = add.call(undefined, 参数)
        React强制把this变成 undefined
        */

        // 这样也是不能修改的
        // this.state.number +=1
        // 必须要用 setState
        this.setState({
            number:this.state.number + 1
        })

    }
    render(){
        return (
            <div>
                app2{this.props.name}
                <div>
                    {this.state.number}
                    <button onClick={this.add.bind(this)}>+</button>
                </div>
            </div>
        )
        // 新手必犯错 onClick = {this.add()}  
        // add()是函数的返回值
        // <button onClick={this.add()}>+</button>

        // 绑定this有两种方式
        /*
        第一种 bind(this)
        onClick={this.add.bind(this)}
        第二种 箭头函数
        onClick={()=>this.add}
        */
    }
}

function render(){
    ReactDOM.render(
        <App2 name="hjx"/>,
        document.querySelector('#root')
    )
}

render();
```

> ### 使用class组件的限制

```
1. 必须继承  React.Component
2. 想自定义属性，必须写 constructor() constructor必须接受一个 props参数
3.  在 constructor里 必须用super(props) 这是 js规定的
4. 必须在 constructor 初始化 state
    constructor(props) {
        super(props);
        this.state = {
            x:0,
            y:1
        }
    }

你的事件可以放到 class内部

你必须要有一个render函数 同时 render必须 返回一个标签 而且这个标签只能有一个根元素

如果你在标签里想用porps必须 是 {this.props.xxx}
如果你想用state 必须是 {this.state.xxx}

如果你想用 onClick 事件 必须 自己绑定this
```

> ### 用class组件实现 box1 box2

```
class Box1 extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            number:0
        }
    }
    add(){
        this.setState({
            number:this.state.number + 1
        })
    }
    minus(){
        this.setState({
            number:this.state.number - 1
        })
    }
    render(){
        return (
            <div>
                Box1-{this.props.name}
                <div>
                    {this.state.number}
                    <button onClick={this.add.bind(this)}>+</button>
                    <button onClick={this.minus.bind(this)}>-</button>
                </div>
            </div>
        )
    }
}

class Box2 extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            number:0
        }
    }
    add(){
        this.setState({
            number:this.state.number + 1
        })
    }
    minus(){
        this.setState({
            number:this.state.number - 1
        })
    }
    render(){
        return (
            <div>
                Box2-{this.props.name}
                <div>
                    {this.state.number}
                    <button onClick={this.add.bind(this)}>+</button>
                    <button onClick={this.minus.bind(this)}>-</button>
                </div>
            </div>
        )
    }
}

function App(props){
    return (
        <div>
            <Box1 name="hjx1"/>
            <Box2 name="hjx2"/>
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
```

> #### 你的疑惑为什么更新数据要 setState 而不是直接改？

```
add(){
    //为什么是 setState
    this.setState({
        number:this.state.number + 1
    })

    /*
    为什么不是这样写，这样更方便理解啊！
    this.state.number += 1
    render()
    */
}
```

> #### setState 优点

- setState可以对更新进行优化，有的时候你自己render很容易 重复调用，这样页面就卡了,减少更新的损耗

    ```
    this.state.number +=1
    render()
    this.state.name = 'aa'
    render()
    this.state.age = 19
    render()
    ```

- setState会将 大批量的更新合并为一次更新

> Box2的 点击 加二 或者 减二

```
// 这个肯定没问题
add(){
    this.setState({
        number:this.state.number + 2
    })
}

// 我能不能分两次 setState呢？
add(){
    this.setState({
        number:this.state.number + 1
    })

    this.setState({
        number:this.state.number + 1
    })
}

// 答案是不行的
假设初始的时候 number = 0
第一次 setState
this.setState({
    number:this.state.number + 1
})
但是第二次 setState 时候 this.state.number到底是0还是1呢？

你会说 不是已经 setState 过了吗？

注意！由于 setState 会合并，它有可能并不会马上执行(异步更新)
注意！由于 setState 会合并，它有可能并不会马上执行(异步更新)
注意！由于 setState 会合并，它有可能并不会马上执行(异步更新)

```

> 如何解决 add函数内两次 setState呢？ —— 函数回调

```
add(){
    this.setState((state)=>{
        return {number:state.number+1}
    })
    
    this.setState((state)=>{
        return {number:state.number+1}
    })
}

// 但是明显你不会这么智障的写代码

add(){
    this.setState({
       number:this.state.number + 2
    })
}
```

### 总结

> 至此我们知道我们有个 应用App 它内部有两个盒子 Box1和 Box2,然后你就去看Box1的实现，又继续去看Box2的实现，此时代码非常的模块化


> 而且 Box1中 点击 add就会 number+=1 然后自己调用 render，而且render只会更新 Box1 里的div 而且只会更新你改的number

> Box1的 render过程每次都会去跟上一次对比 发现 span里的number变了 ,那么就找到这个位置只改 <span>内的 {this.state.number}

#### 寻找两次render结果不同之处的过程 叫做—— DOM diff

- 如果面试官问你 dom diff的细节，就要看给多少工资 你给10K以下，你就呸，我会这个还跟你来面试


[代码仓库](https://github.com/slTrust/React-study)
