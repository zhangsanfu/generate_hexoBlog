---
title: React入门006之ContextApi03换肤功能
date: 2019-01-17 22:08:43
tags: React入门
---

### 换肤功能

- [在线代码](https://codesandbox.io/s/l7k39r562q)

index.js

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

const themeContext = React.createContext();

function Button() {
  return <button>button</button>;
}

function Input() {
  return <input />;
}

function Box(props) {
  return <div className={`box ${props.theme}`}>{props.children}</div>;
}

class App extends React.Component {
  constructor() {
    super();
    this.state = {
      theme: "light"
    };
  }
  change() {
    let res = this.state.theme === "green" ? "red" : "green";
    this.setState({
      theme: res
    });
  }
  render() {
    return (
      <themeContext.Provider value={this.state.theme}>
        <div className="App">
          <button onClick={this.change.bind(this)}>换肤</button>
          <themeContext.Consumer>
            {theme => {
              return (
                <div>
                  <Box theme={theme}>
                    <Button />
                  </Box>
                  <Box theme={theme}>
                    <Input />
                  </Box>
                </div>
              );
            }}
          </themeContext.Consumer>
        </div>
      </themeContext.Provider>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

style.css

```
.App {
  font-family: sans-serif;
  text-align: center;
}

.box.green {
  border: 1px solid green;
}

.box.red {
  border: 1px solid red;
}

```