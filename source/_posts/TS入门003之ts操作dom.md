---
title: TS入门003之ts操作dom
date: 2019-02-02 10:58:49
tags: TS入门
---

### 做一个计算器功能

- 安装 本地server
- 新建目录 ts003

```
npm i http-server -g

# 开启服务在目录 ts003
http-server -c-1 
```

- 初始化代码

ts003/index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
<script src="./caculator.js"></script>
</body>
</html>
```

ts003/caculator.ts

```
{
    let a:number = 1;
    console.log(a);
}
```

- 本地实时编译ts

```
# 注意是当前目录  ts003
tsc -w caculator.ts
```

这样你把 http-server 开启的端口，在浏览器打开，就能看到打印1

> 你期望这样

```
html里直接运行 ts   但是这样是走不通的

所以

html里只能运行js, 把ts编译为js
```

### 开始做一个计算器

> #### 1.用ts生成 button 添加到页面

caculator.ts

```
{   
    // 这是js
    // let button = document.createElement('button');
    // 使用 ts
    let button:HTMLButtonElement = document.createElement('button');
    button.textContent = '0';
    document.body.appendChild(button);
}
```

> #### 2.函数提取

```
{   
    function createButton(text:string){
        let button:HTMLButtonElement = document.createElement('button');
        button.textContent = text;
        document.body.appendChild(button);
    }

    createButton('0')
    createButton('1')
    createButton('2')
    createButton('3')
    createButton('4')
    createButton('5')
    createButton('6')
    createButton('7')
    createButton('8')
    createButton('9')

    // '0' 表示 字符串
    // 0 表示 数字
    // 如果你瞎搞 ts 就弄死你
}
```

> #### 3.继续优化

```
// 造出所有按钮
{   
    function createButton(text:string){
        let button:HTMLButtonElement = document.createElement('button');
        button.textContent = text;
        document.body.appendChild(button);
    }

    `clear,/,
    7,8,9,*,
    4,5,6,-,
    1,2,3,+,
    0,.,=`.split(',').forEach(string=>{
        createButton(string);
    })
}
```

> #### 4.我们希望按钮换行 就跟计算器那样排列

- 二维数组  
- TS如何定义二维数组呢？

```
//二位数组 ——  Array<Array<string>>

{   
    function createButton(text:string,container:HTMLElement){
        let button:HTMLButtonElement = document.createElement('button');
        button.textContent = text;
        container.appendChild(button);
    }

    let keys:Array<Array<string>> = [
        ["clear", "/"],
        ["7", "8", "9", "*"], 
        ["4", "5", "6", "-"], 
        ["1", "2", "3", "+"],
        [ "0", ".", "="]
    ];

    keys.forEach( ( textList:Array<string> )=>{
        let div:HTMLDivElement = document.createElement('div');
        textList.forEach( (text:string)=>{
            createButton(text,div);
        })
        document.body.appendChild(div);
    });
}
```

> #### 5.优化一下样式

- 改变一下层级结构
- 添加一些类名

caculator.ts

```
// 添加样式
{   
    function createButton(text:string,container:HTMLElement,className:string){
        let button:HTMLButtonElement = document.createElement('button');
        button.textContent = text;
        if(className){
            button.className = className;
        }
        container.appendChild(button);
    }

    // 让计算器有个容器而不是直接塞到 body里
    let container:HTMLDivElement = document.createElement('div');
    container.classList.add('caculator');
    document.body.appendChild(container);
    // 显示结果的地方
    let output:HTMLDivElement = document.createElement('div');
    output.classList.add('output');
    let span:HTMLSpanElement = document.createElement('span');
    span.textContent = '0';
    output.appendChild(span);
    container.appendChild(output);

    let keys:Array<Array<string>> = [
        ["clear", "/"],
        ["7", "8", "9", "*"], 
        ["4", "5", "6", "-"], 
        ["1", "2", "3", "+"],
        [ "0", ".", "="]
    ];

    keys.forEach( ( textList:Array<string> )=>{
        let div:HTMLDivElement = document.createElement('div');
        div.classList.add('row');
        textList.forEach( (text:string)=>{
            // 添加样式
            createButton(text,div,`button text-${text}`);
        })
        container.appendChild(div);
    });
}
```

caculator.css

```
.caculator{
    /* 让元素内缩 不然output就占满了 */
    display: inline-block;
}
.caculator .button{
    height: 6em;
    width:6em;
}
.caculator .button.text-clear{
    width:18em;
}

.caculator .button.text-0{
    width:12em;
}

.caculator .output{
    border:1px solid grey;
    height: 6em;
    position: relative;
}

.caculator .output span{
    position: absolute;
    right:.4em;
    bottom:.4em;
    font-size: 40px;
}
```


> #### 6.事件监听

- 放弃监听每一个button
- 用事件委托，监听容器

```
container.addEventListener('click',function(event){
    console.log(event.target);
});
```

> #### 7.处理计算

```
{   
    // 声明创建函数
    function createButton(text:string,container:HTMLElement,className:string){
        let button:HTMLButtonElement = document.createElement('button');
        button.textContent = text;
        if(className){
            button.className = className;
        }
        container.appendChild(button);
        return button;
    }

    // 让计算器有个容器而不是直接塞到 body里
    let container:HTMLDivElement = document.createElement('div');
    container.classList.add('caculator');
    document.body.appendChild(container);
    
    // 显示结果的地方
    let output:HTMLDivElement = document.createElement('div');
    output.classList.add('output');

    // 创建 output 里的 span
    let span:HTMLSpanElement = document.createElement('span');
    span.textContent = '0';
    output.appendChild(span);
    container.appendChild(output);

    // 三个变量  n1 n2 数字  operator 操作符
    let n1:number
    let n2:number
    let operator:string

    container.addEventListener('click',function(event){
        if( event.target instanceof HTMLButtonElement){
            let button:HTMLButtonElement = event.target;
            let text:string = button.textContent
            //看 text 是不是0-9的数字
            if('0123456789'.indexOf(text)>=0){
                console.log('数字')
                // 如果已经有操作符就操作n2
                if(operator){
                    if(n2){
                        n2 = parseInt(n2.toString() + text);
                    }else{
                        n2 = parseInt(text);
                    }
                    span.textContent = n2.toString();
                }else{
                    if(n1){
                        n1 = parseInt(n1.toString() + text);
                    }else{
                        n1 = parseInt(text);
                    }
                    span.textContent = n1.toString();
                }
                
            }else if('+*-/'.indexOf(text)>=0){
                console.log('操作符')
                operator = text;
            }else if('='.indexOf(text)>=0){
                let result:number;
                if(operator === '+'){
                    result = n1 + n2
                }else if(operator === '-'){
                    result = n1 - n2
                }else if(operator === '*'){
                    result = n1 * n2
                }else if(operator === '/'){
                    result = n1 / n2
                }

                span.textContent = result.toString();
            }else{
                console.log('不知道')
            }
            console.log(n1,operator,n2)
        }
    });

    let keys:Array<Array<string>> = [
        ["clear", "/"],
        ["7", "8", "9", "*"], 
        ["4", "5", "6", "-"], 
        ["1", "2", "3", "+"],
        [ "0", ".", "="]
    ];

    keys.forEach( ( textList:Array<string> )=>{
        let div:HTMLDivElement = document.createElement('div');
        div.classList.add('row');
        textList.forEach( (text:string)=>{
            // 添加样式
            createButton(text,div,`button text-${text}`);
        })
        container.appendChild(div);
    });
}
```

> #### 8.class使用


```
// class 使用
{   
    class Caculator{
        public container:HTMLDivElement;
        private output:HTMLDivElement;
        private span:HTMLSpanElement;
        public n1:number;
        public n2:number;
        public operator:string;
        public keys:Array<Array<string>> = [
            ["clear", "/"],
            ["7", "8", "9", "*"], 
            ["4", "5", "6", "-"], 
            ["1", "2", "3", "+"],
            [ "0", ".", "="]
        ];

        constructor(){
            this.createContainer();
            this.createOutput();
            this.createButtons();
            this.bindEvents();
        }
        createButton(text:string,container:HTMLElement,className:string){
            let button:HTMLButtonElement = document.createElement('button');
            button.textContent = text;
            if(className){
                button.className = className;
            }
            container.appendChild(button);
            return button;
        }
        createContainer(){
            let container:HTMLDivElement = document.createElement('div');
            container.classList.add('caculator');
            document.body.appendChild(container);
            this.container = container;
        }
        createOutput(){
            let output:HTMLDivElement = document.createElement('div');
            output.classList.add('output');

            let span:HTMLSpanElement = document.createElement('span');
            span.textContent = '0';
            output.appendChild(span);

            this.output = output;
            this.span = span;
            this.container.appendChild(output);
        }
        createButtons(){
            this.keys.forEach( ( textList:Array<string> )=>{
                let div:HTMLDivElement = document.createElement('div');
                div.classList.add('row');
                textList.forEach( (text:string)=>{
                    // 添加样式
                    this.createButton(text,div,`button text-${text}`);
                })
                this.container.appendChild(div);
            });
        }
        bindEvents(){
            this.container.addEventListener('click',(event)=>{
                if( event.target instanceof HTMLButtonElement){
                    let button:HTMLButtonElement = event.target;
                    let text:string = button.textContent
                    //看 text 是不是0-9的数字
                    if('0123456789'.indexOf(text)>=0){
                        console.log('数字')
                        // 如果已经有操作符就操作n2
                        if(this.operator){
                            if(this.n2){
                                this.n2 = parseInt(this.n2.toString() + text);
                            }else{
                                this.n2 = parseInt(text);
                            }
                            this.span.textContent = this.n2.toString();
                        }else{
                            if(this.n1){
                                this.n1 = parseInt(this.n1.toString() + text);
                            }else{
                                this.n1 = parseInt(text);
                            }
                            this.span.textContent = this.n1.toString();
                        }
                        
                    }else if('+-*/'.indexOf(text)>=0){
                        console.log('操作符')
                        this.operator = text;
                    }else if('='.indexOf(text)>=0){
                        let result:number;
                        if(this.operator === '+'){
                            result = this.n1 + this.n2;
                        }else if(this.operator === '-'){
                            result = this.n1 - this.n2;
                        }else if(this.operator === '*'){
                            result = this.n1 * this.n2;
                        }else if(this.operator === '/'){
                            result = this.n1 / this.n2;
                        }
        
                        this.span.textContent = result.toString();
                    }else{
                        console.log('不知道')
                    }
                    console.log(this.n1,this.operator,this.n2);
                }
            });
        }
    }

   new Caculator();
}
```

### 优化

```
{   
    class Caculator{
        public container:HTMLDivElement;
        private output:HTMLDivElement;
        private span:HTMLSpanElement;
        public n1:string = null; // 用 number 导致 1.0 会变成 1
        public n2:string = null;
        public operator:string = null;
        public result:string = null;
        public keys:Array<Array<string>> = [
            ["clear", "/"],
            ["7", "8", "9", "*"], 
            ["4", "5", "6", "-"], 
            ["1", "2", "3", "+"],
            [ "0", ".", "="]
        ];

        constructor(){
            this.createContainer();
            this.createOutput();
            this.createButtons();
            this.bindEvents();
        }
        createButton(text:string,container:HTMLElement,className:string){
            let button:HTMLButtonElement = document.createElement('button');
            button.textContent = text;
            if(className){
                button.className = className;
            }
            container.appendChild(button);
            return button;
        }
        createContainer(){
            let container:HTMLDivElement = document.createElement('div');
            container.classList.add('caculator');
            document.body.appendChild(container);
            this.container = container;
        }
        createOutput(){
            let output:HTMLDivElement = document.createElement('div');
            output.classList.add('output');

            let span:HTMLSpanElement = document.createElement('span');
            span.textContent = '0';
            output.appendChild(span);

            this.output = output;
            this.span = span;
            this.container.appendChild(output);
        }
        createButtons(){
            this.keys.forEach( ( textList:Array<string> )=>{
                let div:HTMLDivElement = document.createElement('div');
                div.classList.add('row');
                textList.forEach( (text:string)=>{
                    // 添加样式
                    this.createButton(text,div,`button text-${text}`);
                })
                this.container.appendChild(div);
            });
        }
        bindEvents(){
            this.container.addEventListener('click',(event)=>{
                if( event.target instanceof HTMLButtonElement){
                    let button:HTMLButtonElement = event.target;
                    let text:string = button.textContent
                    this.updateNumberOrOperator(text);
                   
                }
            });
        }
        updateNumber(name:string,text:string):void{
            if(this[name]){
                this[name] += text;
            }else{
                this[name] = text;
            }
            this.span.textContent = this[name].toString();
        }
        updateNumbers(text:string):void{
            if(this.operator){
                this.updateNumber('n2',text);
            }else{
                this.updateNumber('n1',text);
            }
        }
        updateResult():void{
            let result;
            let n1:number = parseFloat(this.n1);
            let n2:number = parseFloat(this.n2);

            if(this.operator === '+'){
                result = n1 + n2;
            }else if(this.operator === '-'){
                result = n1 - n2;
            }else if(this.operator === '*'){
                result = n1 * n2;
            }else if(this.operator === '/'){
                result = n1 / n2;
            }
            this.span.textContent = result.toString();
            // 结果出来后重置 n1 n2
            this.n1 = null;
            this.n2 = null;
            this.operator = null;
            this.result = result;
        }
        updateOperator(text:string):void{
            //  点符号时候 没点过数字
            if(this.n1 === null){
                this.n1 = this.result;
            }
            this.operator = text;
        }
        updateNumberOrOperator(text:string){
            if('0123456789.'.indexOf(text)>=0){
                this.updateNumbers(text);
            }else if('+*-/'.indexOf(text)>=0){
                this.updateOperator(text);
            }else if('='.indexOf(text)>=0){
                this.updateResult();
            }else if(text==='clear'){
                this.n1 = null;
                this.n2 = null;
                this.operator = null;
                this.result = null;
                this.span.textContent = '0';
            }else{
                console.log('不知道')
            }
        }
    }

   new Caculator();
}
```

