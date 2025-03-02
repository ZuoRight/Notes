---
comments: true
hide:
  - feedback
---

# 引言

- <https://github.com/WTFAcademy/WTF-JavaScript/tree/main>
- <https://zh.javascript.info>

JavaScript 最初由 Netscape 公司 的 Brendan Eich（布兰登·艾希）在 1995 年开发，仅用了10天，所以有很多设计上的缺陷。

最初起名为 Mocha，后改为 LiveScript，为了蹭 Java 的流量最后改为 JavaScipt。

JS 本身是运行在前端浏览器中的语言，但 Node.js 为 JS 提供了运行时环境，使它可以运行在后端。

也可以在任意搭载了 JS 引擎（也可称作JS虚拟机）的设备中执行，比如 V8、SpiderMonkey

## ES6

ECMAScript，简称 ES，是 JS 的语言规范

> ECMA(European Computer Manufacturers Association) 欧洲计算机制造商协会

ES6（ECMAScript 2015）是 JavaScript 语言的一个重要的更新，引入了许多新的特性和语法，使得 JavaScript 更加强大和灵活。

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

## 数据类型

在 JS 中，除了原始类型外，几乎所有东西都是对象。

可以用操作符 `typeof x` 查看数据类型，也可以写成 `typeof(x)`，不过这里的 `()` 只是分组的作用，并不是函数。

### 原始类型

也叫基础类型，或值类型。值类型存储在栈内存中。

把一个值类型的变量赋值给另一个变量时，是一个完整的自动复制过程。

```js
typeof undefined // "undefined"

typeof null // "object" null其实是一个单独的类型，并不属于object，这是JS语言的一个错误

typeof 0 // "number"

typeof 10n // "bigint"

typeof "foo" // "string"

typeof true // "boolean"

typeof Symbol("id") // "symbol"
```

```js
// 判断是否为整数
Number.isInteger(42);  // true
Number.isInteger(42.3);  // false

// 判断true或false
Boolean(0)  // false

isFinite( )  // 判断某个值是否为正常数值
isNaN( )  // 判断某个值是否为NaN

// 判断一个对象是否是其任意父类派生的实例
console.log([] instanceof Object);  // true
```

### 对象类型

实际上包括了所有非原始类型，比如 Array、Function、Promise 等，以及浏览器 DOM 和 API 等。

对象类型存储在堆内存中，准确的说存储的是指向堆内存中该值的指针，可以动态地添加、修改或删除。

在赋值时不会自动复制，而是引用，如果想复制后的新对象不被原对象影响，需要使用深拷贝。

```js
typeof [1, 2, 3]  // 'object'
typeof Math // "object" Math是内置的object

// 其实并没有函数类型，函数隶属于object类型
// 查看函数的类型返回function属于JS语言的错误，不过这个错误恰好方便一些情况的使用
typeof alert // "function"
```

### 类型转换

```js
parseInt("string")  // 将字符串转换为整数
parseFloat("string")  // 将字符串转换为浮点数
```

### 深拷贝

```js
let x = {
    name: "wtf",
    age: 18,
    arr: [],
    obj: {
        a: 1,
    }
};

let y = JSON.parse(JSON.stringify(x));  // 深拷贝
y.obj.a = 2;  // 不会影响 x.obj.a
```
