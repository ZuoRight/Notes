# JavaScript

## 变量

```js
let x = "hello world"  // JS语句可以用`;`结尾，也可以忽略
let [x, y, z] = ['hello', 'JavaScript', 'ES6'];  // 解构赋值：同时给多个变量赋值
const PI=3.14;

console.log(x)  // 打印
```

运行脚本：`node demo.js`

## 函数

### 输出函数

```js
alert('Hello World');

// 控制台输出log
console.log("hello %s", "7c")  // 格式化：%s 字符，%d 整数，%f 浮点数，%o 对象
```

### 普通函数

```js
function demo() {
    let x = 5;
    console.log(x);
}
demo();
```

### 匿名函数

```js
// 匿名函数（注意末尾要带分号，表示赋值语句结束）
var foo = function (x) {
    pass;
    return {
        xxx
    };
};

data: function () { pass } 可简写为 data () { pass }

// 箭头函数
x => {
    pass;
    return;
}
```

### 异步函数

```js
async function demo() {
    // await关键字告诉任何基于Promise的函数，要等待promise变成fulfilled(履行)或rejected(拒绝)
    await A();
    await B();
    C();  // 等待A和B执行完再执行C
}

function A() {
    return Promise(xxx)
}


// 等待函数执行完成并打印出其中的错误
demo()
    .then(() => process.exit(0))
    .catch((error) => {
        console.error(error);
        process.exit(1);
    });
```

### 函数传参

如果只想给其中某个参数传入值，而其他参数使用默认值

- 方式1：undefined

```js
function greet(greeting = "Hello", name = "World") {
  console.log(`${greeting}, ${name}!`);
}

greet(undefined, "John"); // 输出：Hello, John!
greet("Hi"); // 输出：Hi, World!
```

- 方式2：对象解构的方式

```js
function greet({ greeting = "Hello", name = "World" }) {
  console.log(`${greeting}, ${name}!`);
}

greet({ name: "John" }); // 输出：Hello, John!
greet({ greeting: "Hi" }); // 输出：Hi, World!

```

## 面向对象

JS没有类的概念

在一个方法内部，this是一个特殊的变量，它始终指向当前对象，可以通过this.x拿到对象的属性

一个只在函数内部起作用的关键字：arguments，通常用来判断函数传入的个数：arguments.length

### 方式1：基于构造函数（constructor）和原型链（prototype）

构造函数就相当于类，就是对象的模板，它就是个普通函数，但有自己的特征和用法，首字母一般大写。构造函数体内部使用了this关键字，代表了所要生成的对象/实例，建议启用严格模式，使函数内部的this不能指向全局对象。

```javascript
var Vehicle = function () {
    'use strict';
    this.price = 1000;
};
```

实例化时要用new命令，即执行构造函数，返回一个实例对象。

```javascript
var v = new Vehicle();  // 也可以不带括号，但建议带上
v.price // 1000
```

### 方式2：Object.create()

没有构造函数时，还可以把现有的对象作为模板生成新的对象。

可以用obj.__proto__的方式将一个对象的原型指向任何对象，但一般不这样做，而是通过创建一个函数，然后使用Object.create()方法指定原型

```javascript
// 原型对象
var Student = {
    pass
};

// 创建一个创建原型的对象
function createStudent(name) {
    var s1 = Object.create(Student);  // 基于Student原型创建一个新对象
    s.name = name;  // 初始化新对象
    return s;
}

//实例化
var xiaoming = createStudent('小明');
```

### 方式3：ES6 中新增了用class定义类

```javascript
class 类名 [extends 继承类] {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

## 提交表单

### 方式1

```html
<form id="test">
    <input type="text" name="test">
    <button type="button" onclick="doSubmitForm()">Submit</button>
</form>

<script>
function doSubmitForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input
    form.submit();  // js提交form，但通常不建议这样
    return true;  // 而是返回true，用form自己的onsubmit方法提交
}
</script>
```

### 方式2：AJAX

Web的运作原理：一次HTTP请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用JavaScript发送这个新请求，接收到数据后，再用JavaScript更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

AJAX全称：Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求，在现代浏览器上写AJAX主要依靠XMLHttpRequest对象。

### 方式3：Promise

Promise有各种开源实现，比如axios

## 错误处理

```text
try ... catch ... finally
```

## 模块 Module

`require` 是用于在 Node.js 中导入模块的函数，属于同步操作

`import` 是用于在 ES6 中导入模块的新关键字，属于异步操作

一个模块就是一个独立的文件，该文件内部的所有变量，外部无法获取，如果希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。

JS本身是没有模块的概念的，ES6 引入，ES6 的模块不是对象，而是通过export命令显式指定输出的代码，再通过import命令引入。

```javascript
export function foo() {
    pass
};

import {foo} from '模块文件的位置';
```

## 参考

- <https://zh.javascript.info/>
- <https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics>
