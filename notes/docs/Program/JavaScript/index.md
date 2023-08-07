# 引言

JavaScript 最初由 Netscape 公司 的 Brendan Eich（布兰登·艾希）在 1995 年开发，仅用了10天，所以有很多设计上的缺陷

最初起名为 Mocha，后改为 LiveScript，为了蹭 Java 的流量最后改为 JavaScipt

JS 本身是运行在前端浏览器中的语言，但 Node.js 为 JS 提供了运行时环境，使它可以运行在后端

> 命令：`node demo.js`

也可以在任意搭载了 JS 引擎（也可称作JS虚拟机）的设备中执行，比如 V8、SpiderMonkey

## ES6

ECMAScript，简称 ES，是 JS 的语言规范

> ECMA(European Computer Manufacturers Association) 欧洲计算机制造商协会

ES6（也被称为 ECMAScript 2015）是 JavaScript 语言的一个重要的更新，引入了许多新的特性和语法，使得 JavaScript 更加强大和灵活。

比如

- 变量声明关键字：let、const
- 解构赋值
- 扩展运算符
- 箭头函数
- Promises 和 异步函数
- 类（class）
- 模块（module）
- 生成器（generator）
- 迭代器（iterator）
- Symbol 类型
- 新的数据结构：Map、Set
- 新的数组和对象的方法

## 模块

JS 本身是没有模块的概念，模块化的演变经历了一个漫长的过程，从最初的 CommonJS ，到后来的 AMD 和 CMD，再到今天的 ES6 模块化方案

- CommonJS

用于在 Node.js 中导入模块的函数，属于同步操作

```js
// lib.js
module.exports = 'Hello!';  // 导出模块

// app.js
let message = require('./lib');  // 导入模块
console.log(message);  // 输出 'Hello!'
```

- ES6 模块

用于在 ES6 中导入模块的新关键字，属于异步操作

```js
export function foo() {
    pass
};

import {foo} from '模块文件的位置';
```

## 参考

- <https://github.com/WTFAcademy/WTF-JavaScript/tree/main>
- <https://zh.javascript.info/>
