# 类与模块

## 类

JS 中的类是基于原型（Prototype）实现的，原型是 JS 中的一个内部对象，它可以让我们共享方法和属性。

ES6 引入了 class 关键字，推荐

### 方式一：class

```js
// 定义一个Person类
class Person {
    static b = 2  // 类属性
    a = 1  // 实例属性

    // 构造器，不是必须要写的，但如果想给实例添加一些自己独有的属性，那么就要写构造器
    constructor(name, age){
        // 构造器中的this，是指类的实例化对象
        this.name = name
        this.age = age
    }

    // 方法不需要 funtion 关键字
    speak(){
        console.log(`我叫${this.name}，我今年${this.age}了`)
    }
}

// 继承
class Student extends Person{
    // 如果当前类没有定义构造器则自动使用父类的构造器
    constructor(name, age, grade){
        super(name, age)  // 如果要新增属性，则需要使用super调用父类的属性
        this.grade = grade
    }

    // 可以重写从父类继承过来的方法
    speak(){
        console.log(`我叫${this.name}，我今年${this.age}了，我读${this.grade}年级了`)
        this.study()
    }
}

// 实例化
const me = new Student('7c', 18, 3);
```

### 方式二：构造函数

```js
// 声明一个函数对象
function Person(name, age) {
  this.name = name;  // this 是一个特殊的变量，它始终指向当前对象
  this.age = age;
}

/*
每个函数在创建时都会有一些默认的属性，比如
    Person.name 函数的名称
    Person.length 函数的参数个数
    Person.__proto__ 函数的原型
    Person.prototype 操作函数的原型
*/

// 给Person原型添加一个方法
Person.prototype.greet = function() {
  console.log(`Hello, 我叫 ${this.name}, 我 ${this.age} 岁了。`);
};

// 通过new关键字实例化一个新对象，也叫构造函数（constructor），名称通常首字母大写
let alice = new Person('Alice', 25);  // 形成一条原型链（__proto__）：null -> Object -> Person -> alice

// alice继承了原型链的属性和方法
alice.greet();  // Hello, 我叫 Alice, 我 25 岁了.
```

### 方式三：`Object.create()`

使用现有对象创建新对象

```js
// 现有对象
let animal = {
    eats: true
};

// 新对象
let rabbit = Object.create(animal);

console.log(rabbit.__proto__ === animal);  // true
console.log(rabbit.eats);  // true
```

## 模块化

JS 本身是没有模块的概念，模块化的演变经历了一个漫长的过程，从最初的 CommonJS ，到后来的 AMD 和 CMD，再到今天的 ES6 模块化方案

### CommonJS

用于在 Node.js 中引入模块的函数，属于同步操作，不推荐！

```js
// app.js
let message = require('./lib');  // 引入模块
console.log(message);  // 输出 'Hello!'
```

```js
// lib.js
module.exports = 'Hello!';  // 导出模块
```

### ES6

使用 `export` 和 `import` 关键字，属于异步操作，推荐！

在 Node 环境下若要使用 import 方式引入，需要将文件扩展名改为 `.mjs `

或者在 `package.json` 中添加配置

```json
{
  "type": "module"
}
```

在浏览器中使用需要

```js
<script type="module" src="demo.js"></script>
```

---

如何引入取决于如何导出

- 默认导出&引入

```js
const person = {name: 'John', age: 30};
export default person;
export default {name: 'John', age: 30};  // 也可以直接导出
```

```js
// 引入默认导出 - 可以使用任意名称
import User from './person.js';  // .js 和 .jsx 后缀可省略，其它后缀不能省略
console.log(User.name);

// 引入默认函数
import myFunction from './utils.js';
myFunction();
```

---

- 命名导出，需使用 `{}`

```js
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

const subtract = (a, b) => a - b;
const multiply = (a, b) => a * b;
export { subtract, multiply };  // 可以一起导出多个

const divide = (a, b) => a / b;
export { divide as division };  // 导出时重命名
```

```js
import * as MathUtils from './math.js';  // 引入所有命名导出
console.log(MathUtils.subtract(5, 2));
console.log(MathUtils.division(10, 2));

import { PI, add } from './math.js';  // 引入特定命名导出
console.log(PI);
console.log(add(1, 2));

import { multiply as mul } from './math.js';
console.log(mul(2, 3));  // 引入时重命名
```

---

```js
// 同时引入默认和命名导出
import App, { version } from "./module.js";

const app = new App();
app.run(); // "App is running"
console.log(version); // "1.0"
```

---

- 动态引入

```js
async function loadModule() {
    const math = await import('./math.js');
    console.log(math.add(2, 3)); // 5
}

loadModule();
```
