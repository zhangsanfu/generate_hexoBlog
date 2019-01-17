---
title: React入门006之ContextApi02
date: 2019-01-17 21:41:14
tags: React入门
---

>  继续上一篇内容 

### ContextApi

> #### 如果我想修改传递的n呢？


```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function F1() {
  return (
    <div className="box">
      1111
      <F2 />
    </div>
  );
}
function F2() {
  return (
    <div className="box">
      2222
      <F3 />
    </div>
  );
}
function F3() {
  return (
    <div className="box">
      3333
      <nContext.Consumer>
        {xxx => <F4 n4={xxx.n} setN={xxx.setN} />}
      </nContext.Consumer>
    </div>
  );
}
function F4(props) {
  return (
    <div className="box">
      4444,{props.n4}
      <button onClick={props.setN}>Click ME</button>
    </div>
  );
}

const nContext = React.createContext();

class App extends React.Component {
  constructor() {
    super();
    this.state = {
      x: {
        n: 100,
        setN: () => {
          this.setState({
            x: {
              ...this.state.x,
              n: this.state.x.n + 1
            }
          });
        }
      }
    };
  }
  render() {
    return (
      <div>
        <nContext.Provider value={this.state.x}>
          <F1 />
        </nContext.Provider>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

- [代码链接](https://codesandbox.io/s/o7opop6m5q)

> 以前我们传递值

- 需要一层一层的传递

> 现在有了 ContextAPI

```
//在根节点包裹一个 nContext
return (
    <div>
    <nContext.Provider value={obj}>
        <F1 />
    </nContext.Provider>
    </div>
);

/*
obj{
    store,
    setStore()
}
*/

// 后代任意层级想要使用  只需要包裹一个 
<nContext.Consumer>
    {(obj)=>{return <div>{obj}</div>}}
</nContext.Consumer>
```


