---
title: TS入门002用TS开发命令行程序
date: 2019-01-29 21:53:53
tags: TS入门
---

> ### 自学知识的误区

- 不要怕(不要因为没学过而不学了，没学才应该学啊)
- 不要妄图系统学习，了解常用功能
- 有目的的学，不要没目的的看书 看10本书

### 带着目的学习

- 写一个命令行工具
- 再写一个
- 回到第一步，直到你觉得你学的不错了

不要去看书，不要去看文档，遇到问题再去看文档，再去google

> 通过解决一个个的小问题来学习 TS

### 最简单的命令行程序

1.新建 001.ts

```
#!/usr/bin/env ts-node
console.log('hello world');
```

第一行不要问为什么之间照抄！——其实就是 shebang 跟python 文件首行一样 代表指定那个命令来执行此脚本

2.添加执行权限并执行

```
# 添加权限
chmod +x 001.ts

# 执行
./001.ts

# 打印
hello world
```

### 脚本升级 可以在后面跟参数

如

```
vue --version

curl -L https://www.baidu.com

git add xx

git commit -m 'sss'
```

> #### process.argv参数

这个变量可以获取到用户输入的这一行内容

新建 002.ts

```
#!/usr/bin/env ts-node
console.log(process.argv);
console.log('hello world');
```

```
chmod +x 001.ts
./002.ts

# 报错了
```

> 新人在学习最怕什么，最怕的不是知识点复杂，而是你告诉我这样可以运行，但是我运行之后呢？——运行不了，这就进行不下去了

我们先看报错信息

```
/usr/local/lib/node_modules/ts-node/src/index.ts:261
    return new TSError(diagnosticText, diagnosticCodes)
           ^
TSError: ⨯ Unable to compile TypeScript:
002.ts(2,13): error TS2304: Cannot find name 'process'.

    at createTSError (/usr/local/lib/node_modules/ts-node/src/index.ts:261:12)
    at getOutput (/usr/local/lib/node_modules/ts-node/src/index.ts:367:40)
    at Object.compile (/usr/local/lib/node_modules/ts-node/src/index.ts:557:11)
    at Module.m._compile (/usr/local/lib/node_modules/ts-node/src/index.ts:439:43)
    at Module._extensions..js (internal/modules/cjs/loader.js:700:10)
    at Object.require.extensions.(anonymous function) [as .ts] (/usr/local/lib/node_modules/ts-node/src/index.ts:442:12)
    at Module.load (internal/modules/cjs/loader.js:599:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
    at Function.Module._load (internal/modules/cjs/loader.js:530:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)
```

> 如果你看不懂

- 一个一个字的查字典

```
# ts 没有能力 编译
TSError: ⨯ Unable to compile TypeScript:
# 再看 第二行 第13个字有问题 报错编号是 TS2304 意思是找不到 process
002.ts(2,13): error TS2304: Cannot find name 'process'.

# 后面是细节
```

> #### 怎么办？

> #### 怎么办？

> #### 怎么办？

### 程序员最6的查 bug 方式就是复制这行错误到 google

```
error TS2304: Cannot find name 'process'.

实际上这是 Node.js 的全局变量，不可能找不到。

这就是 TS 的厉害之处：如果你不告诉我 process 是什么，我就不允许你用 process。

那么如何告诉 TS process 是什么呢？
```

得到内容挨个点进去看

过程：

1. 复制 error TS2304: Cannot find name 'process'. 到 Google
2. 找到 Stackoverflow 上的一篇[问答](https://stackoverflow.com/questions/35551185/read-arguments-from-command-line-error-ts2304-cannot-find-name-process)
3. 得知要安装 Typings，于是点开[他给的链接](https://www.npmjs.com/package/typings)
4. 看到页面上方显示 Typings is deprecated in favor of NPM @types
5. 得知 Typings 已经被弃用了，但是这货不告诉我新版在哪
6. 猜测新版是 @types/node （纯经验）
7. 运行 npm install @types/node 然后运行 ./2.ts 发现成功
8. 去 Stackoverflow 回复一下，以防别人也遇到不知道新版名称
9. 回复的时候发现已经有人回复了 with typescript 2, its now npm i -D @types/node
10. 发现他用了 -D 选项但是我没有使用，根据我对 npm 的了解，加不加 -D 都行，加上更好一点。

```
# 复制到刚才的目录里执行
npm i -D @types/node
```

结果又警告

```
npm WARN saveError ENOENT: no such file or directory, open '/Users/huangjiaxi/Desktop/typescript-demo/ts002/package.json'
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN enoent ENOENT: no such file or directory, open '/Users/huangjiaxi/Desktop/typescript-demo/ts002/package.json'
npm WARN ts002 No description
npm WARN ts002 No repository field.
npm WARN ts002 No README data
npm WARN ts002 No license field.

+ @types/node@10.12.19
added 1 package from 33 contributors and audited 1 package in 2.191s
found 0 vulnerabilities
```

意思是没有 package.json,我这目录确实没有，那就创建一个


```
# 最快创建 package.json 的方法
npm init -y

# 重新运行
npm i -D @types/node

# 此时就单独 警告  没有描述 没有远程仓库
npm WARN ts002@1.0.0 No description
npm WARN ts002@1.0.0 No repository field.

+ @types/node@10.12.19
updated 1 package and audited 1 package in 1.66s
found 0 vulnerabilities
```

> ### 再次执行 002.ts

```
./002.ts


# 打印如下信息
[ 'node',
  '/Users/huangjiaxi/Desktop/typescript-demo/ts002/002.ts' ]
hello world

/*
[ 'node','/Users/huangjiaxi/Desktop/typescript-demo/ts002/002.ts' ]
这个 node 就是 002.ts 的第一行 告诉这个程序用什么来运行
第二个就是 当前文件的绝对路径
*/
```

添加自己的参数

```
./002.ts  xxx yyy zzz


# 打印
[ 'node',
  '/Users/huangjiaxi/Desktop/typescript-demo/ts002/002.ts',
  'xxx',
  'yyy',
  'zzz' ]
  hello world
```

### 两数之和的程序

新建 003add.ts

```
#!/usr/bin/env ts-node
console.log( process.argv );
let a = process.argv[2];
let b = process.argv[3];

console.log( a + b );
```

执行

```
chomd +x 003add.ts
./003add.ts  1 2

# 得到结果
[ 'node',
  '/Users/huangjiaxi/Desktop/typescript-demo/ts002/003add.ts',
  '1',
  '2' ]
12

# 为啥是 12
你可以打印 process.argv 来看内容 数组里的内容是 字符串啊

# 所以我们要解决这个类型问题
```

004add.ts

```
#!/usr/bin/env ts-node
let a:number = parseInt( process.argv[2] );
let b:number = parseInt( process.argv[3] );

console.log( a + b );
```

执行

```
chmod +x 004add.ts
./004add.ts 1 2

# 打印 
3

# 但是如果这样执行呢？
./004add.ts 1 s
竟然得到了 NaN
```

> 然后聪明的你肯定用 Number.isNaN() 判断

- 005add.ts

```
#!/usr/bin/env ts-node
let a:number = parseInt( process.argv[2] );
let b:number = parseInt( process.argv[3] );

if(Number.isNaN(a)||Number.isNaN(b)){
    console.log('出错啦！')
}

console.log( a + b );
```

```
chmod +x 005add.ts
./005add.ts 1 s

# 又报错了

 return new TSError(diagnosticText, diagnosticCodes)
           ^
TSError: ⨯ Unable to compile TypeScript:
005add.ts(5,11): error TS2339: Property 'isNaN' does not exist on type 'NumberConstructor'.
005add.ts(5,28): error TS2339: Property 'isNaN' does not exist on type 'NumberConstructor'.

    at createTSError (/usr/local/lib/node_modules/ts-node/src/index.ts:261:12)
    at getOutput (/usr/local/lib/node_modules/ts-node/src/index.ts:367:40)
    at Object.compile (/usr/local/lib/node_modules/ts-node/src/index.ts:557:11)
    at Module.m._compile (/usr/local/lib/node_modules/ts-node/src/index.ts:439:43)
    at Module._extensions..js (internal/modules/cjs/loader.js:700:10)
    at Object.require.extensions.(anonymous function) [as .ts] (/usr/local/lib/node_modules/ts-node/src/index.ts:442:12)
    at Module.load (internal/modules/cjs/loader.js:599:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
    at Function.Module._load (internal/modules/cjs/loader.js:530:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)

-----------------------------------------------
# 复制这行 error 到 google

error TS2339: Property 'isNaN' does not exist on type 'NumberConstructor'.

在给出的链接里 看信息
# 我搜到一个信息是
These are part of the es2015 library. make sure you have --lib es2015

意思是 确保你又 es2015

最终我找到的结果就是 添加一个 tsconfig.json 文件 在里面进行 es语法的配置

里面内容如何填写呢？

```

> 直接google  tsconfig.json

得到如下链接
[https://www.typescriptlang.org/docs/handbook/tsconfig-json.html](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

里面有一个 tsconfig.json

```
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "sourceMap": true
    },
    "files": [
        "core.ts",
        "sys.ts",
        "types.ts",
        "scanner.ts",
        "parser.ts",
        "utilities.ts",
        "binder.ts",
        "checker.ts",
        "emitter.ts",
        "program.ts",
        "commandLineParser.ts",
        "tsc.ts",
        "diagnosticInformationMap.generated.ts"
    ]
}
```

> 添加一个 lib 参数

```
{
    "compilerOptions": {
       "lib":[
           "es2015"
       ]
    },
}
```

再次运行刚才的出错文件

```
./005add.ts 1 s

# 打印
出错啦
NaN
```

> 为防止以后出错，多写几个 es 版本

```
{
    "compilerOptions": {
       "lib":["es2015","dom","es2016","es2018"]
    },
}
```

即使这样你发现 Number.isNaN 下面还是用红线，那就 重新进入 vscode

> 再看我们代码存在的问题

```
# 打印
出错啦
NaN

# 已经出错啦，就没必要往后执行了，直接退出程序
但是你不能用 return （return用在函数内部才行） 
```

- 006add.ts

```
#!/usr/bin/env ts-node
let a: number = parseInt( process.argv[2] );
let b: number = parseInt( process.argv[3] );

if(Number.isNaN(a)||Number.isNaN(b)){
    console.log('出错啦！');
    process.exit(1);
}

console.log( a + b );
process.exit(0);
```

- 为啥退出要传递 数字呢

> 进程退出的潜规则——程序员的约定

- 返回 0 代表正常退出
- 返回 非 0 代表错误退出

幸福的家庭都是相似的，不幸的家庭各有各的不幸

```
process.exit(1);
process.exit(2);
process.exit(3);

process.exit(0);
```

> 自行完成

- 加减乘除代码逻辑

### 族谱

100tree.ts

```
#!/usr/bin/env ts-node
function createTabs(n: number): string {
  return '----'.repeat(n);
}
class Person {
  public children: Person[] = [];
  constructor(public name) {}
  addChild(child: Person): void {
    this.children.push(child);
  }
  introduceFamily(n?: number): void {
    n = n || 0;
    console.log(`${createTabs(n)}${this.name}`);
    this.children.forEach(person => {
      person.introduceFamily(n + 1);
    });
  }
}

const grandPa = new Person('王麻子');
const person1 = new Person('王大锤');
const person2 = new Person('王者');
const child11 = new Person('王毛');
const child12 = new Person('王水');
const child21 = new Person('王荣耀');
const child22 = new Person('王农药');

grandPa.addChild(person1);
grandPa.addChild(person2);

person1.addChild(child11);
person1.addChild(child12);
person2.addChild(child21);
person2.addChild(child22);

grandPa.introduceFamily();

王麻子
----王大锤
--------王毛
--------王水
----王者
--------王荣耀
--------王农药
```

> 坑点

- Person[] 规定数组类型要 进行初始化

```
public children: Person[];

# 如果这样声明 只是声明了 Person 的数组，但是没有实例化，也就无法调用 push()
```

- 可选参数 introduceFamily(n?: number): void {...}
- 无返回值 void

```
// n 可以不传递
introduceFamily(n?: number): void {...}
```

- 代码库[https://github.com/slTrust/ts-study](https://github.com/slTrust/ts-study)
