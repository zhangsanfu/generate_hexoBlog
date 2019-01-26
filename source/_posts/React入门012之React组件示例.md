---
title: React入门012之React组件示例
date: 2019-01-26 20:09:09
tags: React入门
---

### React组件

```
cd ~/Desktop

create-react-app react-button-demo


cd react-button-demo

yarn start
```

styles.css

```
.App {
  text-align: center;
}

.App button{
  border:1px solid red;
  background: red;;
  color:#fff;
  padding:3px 10px;
  box-shadow: 0 0 3px rgba(0,0,0,0.2);
  margin:4px;
  border-radius: 4px;
}
```

App.js

```
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <button>Hello</button>
        <button>Hello</button>
      </div>
    );
  }
}

export default App;
```

> 此时如果 另一个组件里想用这个 button 唯一的办法就是把 样式copy一份 button

> 第一个组件 BoxOne

BoxOne.js

```
import React, { Component } from 'react';
import './BoxOne.css';

class BoxOne extends Component {
  render() {
    console.log(this.props.firstLine)
    return (
        <div className="BoxOne">
            <div> {this.props.firstLine || 'Box 1'} </div>
        </div>
    );
  }
}

export default BoxOne;
```

BoxOne.css

```
.BoxOne{
    border:1px solid green;
}
```

App.js

```
import React, { Component } from 'react';
import './App.css';

import BoxOne from './BoxOne'

class App extends Component {
  render() {
    return (
      <div className="App">
        <button className="btn">Hello</button>
        <button className="btn">Hello</button>
        <BoxOne firstLine="Hello" />
        <BoxOne/>
      </div>
    );
  }
}

export default App;

```

> ### 组件的好处是什么？

- 当你的组件不复杂时 你直接写一遍就好了
- 但如果组件重复的使用了  100次呢？
- 如果后期改样式  你是不是得改1000次？

1. 不需要 ctrl + c / ctrl + v
2. 多处用 一个 标签
3. 要改，只改一个地方
4. 个性化只需要传递参数

### 写一个button组件

- src下新建 components 目录
- src/components/button.js

button.js

```
import React, { Component } from 'react';
import './button.css'
class Button extends Component {
  render() {
    return (
        <button className="button2">{this.props.value}</button>
    );
  }
}

export default Button;
```

button.css

```
.button2{
    box-sizing: border-box;
    height: 32px;
    background: #fff;
    border:1px solid rgba(153,153,153,1);
    border-radius: 4px;
    padding:4px 1em;
    margin:10px;
}

.button2:focus{
    outline: none;
    border-color: black;
}
```

App.js

```
import React, { Component } from 'react';
import './App.css';
import Button from './components/button'

class App2 extends Component {
  render() {
    return (
      <div className="App">
        <Button value="按钮"/>
        <Button value="按钮"/>
        <Button value="按钮"/>
      </div>
    );
  }
}

export default App2;
```

### 做个涟漪效果

button.css

```
.button2{
    box-sizing: border-box;
    height: 32px;
    background: #fff;
    border:1px solid rgba(153,153,153,1);
    border-radius: 4px;
    padding:4px 1em;
    margin:10px;
    position: relative;
    overflow: hidden;
}

.button2:focus{
    outline: none;
    border-color: black;
}

.button2 > .circle{
    border-radius: 50%;
    height: 10px;
    width: 10px;
    background: red;
    display: inline-block;
    animation: 1s big;
    position: absolute;
}

@keyframes big{
    0%{
        transform: scale(0.1);
    }
    100%{
        transform: scale(10);
    }
}
```

button.js

```
import React, { Component } from 'react';
import './button.css'

class Button extends Component {
    constructor(props){
        super(props);
        this.state = {
            active:false,
        }
    }
    x(){
        this.setState({
            active:true,
        })
    }
    render() {
        return (
            <button className="button2"
                onClick={ this.x.bind(this) }
            >
                { this.props.value }
                { (this.state.active === true)?(
                    <span className="circle"></span>
                ):''}
            </button>
        );
    }
}

export default Button;
```

> ### 此时我希望涟漪出现的地方是我点击的地方

button.js

```
import React, { Component } from 'react';
import './button.css'

class Button extends Component {
    constructor(props){
        super(props);
        this.state = {
            active:false,
            deltaX:0,
            deltaY:0
        }
        // 当前节点
        this.myRef = React.createRef();
    }
    x(event){
        // 事件源
        console.log(event);
        // 距离左边的距离
        console.log(event.clientX);
        // 距离上面的距离
        console.log(event.clientY);
        // 当前按钮的引用
        console.log(this.myRef);
        // 获取边缘属性
        console.log(this.myRef.current.getBoundingClientRect())
        // 
        let {x,y} = this.myRef.current.getBoundingClientRect();
        let {clientX,clientY} = event;
        let deltaX = clientX - x - 5;
        let deltaY = clientY - y - 5;
        // 得到点击的位置
        console.log(deltaX,deltaY);

        this.setState({
            active:true, 
            deltaX:deltaX,
            deltaY:deltaY
        })
    }
    y(){
        this.setState({
            active:false
        })
    }
    render() {
        return (
            <button ref={this.myRef} className="button2"
                onClick={ this.x.bind(this) }
            >
                <span className="value">{ this.props.value }</span>
               
                { (this.state.active === true)?(
                    <span className="circle" onAnimationEnd={ this.y.bind(this) } style={{left:this.state.deltaX,top:this.state.deltaY}}></span>
                ):''}
            </button>
        );
    }
}

export default Button;
```

button.css

```
.button2{
    box-sizing: border-box;
    height: 32px;
    background: #fff;
    border:1px solid rgba(153,153,153,1);
    border-radius: 4px;
    padding:4px 1em;
    margin:10px;
    position: relative;
    overflow: hidden;
}

.button2:focus{
    outline: none;
    border-color: black;
}

.button2 > .circle{
    border-radius: 50%;
    height: 10px;
    width: 10px;
    background: #ddd;
    opacity: 0.5;
    display: inline-block;
    /* 结束后不恢复原状 */
    animation: 1s big forwards;
    position: absolute;
    z-index: 0;
}
.button2 > .value{
    position: relative;
    z-index: 1;
}

@keyframes big{
    0%{
        transform: scale(0.1);
    }
    100%{
        transform: scale(10);
    }
}
```

- [https://github.com/slTrust/React-study](项目地址)