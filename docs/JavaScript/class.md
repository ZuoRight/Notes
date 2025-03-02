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

用于在 Node.js 中导入模块的函数，属于同步操作，不推荐！

```js
// app.js
let message = require('./lib');  // 导入模块
console.log(message);  // 输出 'Hello!'
```

```js
// lib.js
module.exports = 'Hello!';  // 导出模块
```

### ES6

用于在 ES6 中导入模块的新关键字，属于异步操作，推荐！

```js
// app.js
import {foo} from '模块文件的位置';
```

```js
// lib.js
export function foo() {
    pass
};
```

在 Node 环境下若要使用 import 方式导入，需要 在 `package.json` 中添加配置

```json
{
  "type": "module"
}
```

在 Web 中需要加 module 属性

```js
<script type="module" src="demo.js"></script>
```
