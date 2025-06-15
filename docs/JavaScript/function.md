# 语句与函数

## 运算符

### 算数运算符

```js
let num = 1 + 1;  // 2，加
let num = 2 - 1;  // 1，减
let num = 1 * 2;  // 2，乘
let num = 5 / 2;  // 2.5，除

let num = 2 ** 3;  // 8，求幂
let num = 5 % 2;  // 0.5，取余
let num = Math.floor(5 / 2);  // 2，取整

// 递增
let x = 1;
let y = x++; // 先赋值，后递增
let z = ++x; // 先递增，后赋值

// 递减
let x = 1;
let y = x--; // 先赋值，后递增
let z = --x; // 先递增，后赋值
```

### 比较运算符

JS是弱类型语言，不同类型比较时可能会存在隐式转换

推荐使用严格比较符（数据类型和值都相同）避免因此带来的隐患

```js
let bool3 = (2 > 1);  // true
let bool4 = (2 < 1);  // flase
let bool5 = (2 >= 1);  // true
let bool6 = (2 <= 1);  // flase

let bool1 = (5 == "5");  // true
let bool2 = (5 != "5");  // false
let bool7 = (5 === "5");  // flase 严格相等
let bool8 = (5 !== "5");  // true 严格不等
```

### 逻辑运算符

```js
true && false;  // 与
true || false;  // 或
!true;  // 非
```

### in

检查某个值或键是否存在

### 扩展运算符

- 数组拆包

```js
let arr = [3,4,5,6]
console.log(arr)  // 普通：[3, 4, 5, 6]
console.log(...arr)  // 拆包：3, 4, 5, 6

// 拼接数组
let arr2 = [1, 2, ...arr, 7]
console.log(arr2)  // [1, 2, 3, 4, 5, 6, 7]
```

- 对象拆包

```js
let obj = {name:'alice', age:18, sex:'女'}
let obj2 = {mz:'bob', nl:19, xb:'男'}

// console.log(...obj)  对象不能直接这么拆
console.log({...obj})  // 要放在 {} 里面拆
const cloneObj = {...obj, name:'张岩'}  // 拆包并给name重新赋值
const cloneObj2 = {...obj, ...obj2}  // 拼接对象
```

- 解构时可使用扩展语法收集剩余元素

```js
const arr = [1, 2, 3, 4];
const [first, ...rest] = arr;  // first = 1, rest = [2, 3, 4]

const obj = { a: 1, b: 2, c: 3 };
const { a, ...restObj } = obj;  // a = 1, restObj = { b: 2, c: 3 }
```

## 条件语句

### `if () {} else {}`

```js
let x = 1;

if (x === 0) {
  console.log("x=0");
} else if (x === 1) {
  console.log("x=1");
} else {
  console.log("x=other");
}
```

### 简写：三元运算符

```js
const r = x > y ? x : y;  // 如果 x>y 则 r=x，否则 r=y
```

### `switch () {}`

```js
switch (x) {
  case 0:
    console.log("0");
    break;  // 每条case必须要有break语句，否则将继续执行后面的语句
  case 1:
    console.log("1");
    break;
  case 2:
    console.log("2");
    break;
  default:
    console.log(">=3");
    break;
}
```

## 循环语句

### for

- 经典 C 风格

```js
for (let i = 0; i < 3; i++) {
  console.log(`i 当前的值为：${i}`);
}
```

- `for...of` 遍历可迭代对象

类似 Python 的 `for...in...`

```js
const arr = [1, 2, 3];
for (const item of arr) {
  console.log(item);
}
```

- `for...in` 遍历对象的可枚举属性

```js
const obj = { a: 1, b: 2 };
for (const key in obj) {
  console.log(key, obj[key]);
}
```

### while

`while (循环条件) {增量表达式}`

```js
let i = 0
while (i < 3) {
  console.log(`i 当前的值为：${i}`)
  i++
}
```

### do while

`do {增量表达式} while (循环条件)`

无论条件是否为真，先执行一次语句，再做判断循环

```js
let i = 0
do {
  console.log(`i 当前的值为：${i}`);
  i++;
} while (i < 3);
```

## 跳转语句

- `break` 跳出循环
- `continue` 跳过本次循环，进入下次循环

在嵌套循环中，直接跳出或者跳到指定标签处，而无需一层层执行。

```js
outerLoop:  // 给循环设置一个标签
for (let i = 0; i < 5; i++) {
  for (let j = 0; j < 5; j++) {
    console.log(`i=${i}, j=${j}`);
    if (i == 2 && j == 2) {
      break outerLoop; // 跳出标签为 outerLoop 的循环
    }
  }
}
```

## 函数

```js
function sum(x=0, y=0) {
    return arguments[0] + y;
    // 如果没有return语句则返回undefined
    // arguments 对象包含所有参数，arguments.length 返回参数个数
}

sum(1, 2);  // 3
sum(1)  // 1，y使用默认值
sum(undefined, 2)  // 2，x使用默认值

sum.name  // 获取函数的名字，匿名函数返回变量名
sum.length  // 返回函数的参数个数，如果有默认值，会返回0
```

- 解构式传参

```js
function sum({x=0, y=0}) {
  return x + y;
}

sum({x:1, y:2})  // 3
```

- 打包

不知道接收多少个变量，可以使用 ... 将参数都打包到一个变量里

```js
// 
function demo(...params) {
    console.log('我收到的参数为：', params)
}
demo(1, 2, 3)
```

- 拆包

使用扩展运算符将数组中的元素展开作为函数的参数

```js
function addNumbers(x, y, z) {
  return x + y + z;
}

const numbers = [1, 2, 3];
const result = addNumbers(...numbers);
console.log(result); // 输出: 6
```

## 匿名函数

### 函数表达式

```js
let sum = function(x, y) {
  return x + y;
};  // 注意末尾要带分号，表示赋值语句结束

data: function () { pass }  // 可简写为 data () { pass }
```

### 箭头函数

ES6 新增，`function()` 可简写为 `() =>`

箭头函数没有自己的 this，会继承外层作用域的 this

```js
let demo = (x, y) => {
    return x + y;
}
// 等价于
let demo = function(x, y) {
    return x + y;
}

// 函数内只有单一表达式时，可省略掉花括号和return
let demo = (x, y) => x + y;
```

## 闭包

函数嵌套时，内部函数被外部函数 return，即可形成一个闭包，无论内部函数是否使用到外部函数的变量

闭包常用于封装私有变量

```js
function createGreeting(greetingPrefix) {
    return function(name) {
        console.log(greetingPrefix + ', ' + name);
    };
}

const greetHello = createGreeting('Hello');
greetHello('John');  // 输出: Hello, John
```

## 内置函数

```js
// alert
alert("Hello");  // 弹出模态窗

// confirm()
let isTrue = confirm("true or false");  // 第一个模态窗，显示：内容、确定按钮、取消按钮
alert(`You select ${isTrue}`);  // 第二个模态窗：确定isTrue=true，取消isTrue=false

// prompt(title, [default])
let age = prompt('How old are you?', 100);  // 第一个模态窗，显示：标题、带默认值的输入框、确定按钮、取消按钮
alert(`You are ${age} years old!`);  // 第一个模态窗：确定age=100，取消age=null
```

## this

- 普通函数调用: this 指向全局对象
    - 严格模式下为 undefined
    - 非严格模式下为 window
- 方法调用: this 指向调用该方法的对象
- 构造函数调用: this 指向新创建的实例
- 箭头函数: this 绑定到定义时所在的作用域，不随调用方式变化

JS 中的 `this` 是一个上下文相关的关键字，它的值取决于函数如何被调用，而不是函数定义的位置

有时不符合我们的预期，就需要改变 this 绑定

### 改变 this 绑定

- `function.call(thisArg, arg1, arg2, ...)`

调用一个函数，并显式指定函数执行时的 this 值（thisArg），同时传入参数

```js
function sayHello(greeting) {
  console.log(`${greeting}, ${this.name}`);
}
const person = { name: "Alice" };
sayHello.call(person, "Hi"); // 输出: Hi, Alice
```

- `function.apply(thisArg, [argsArray])`

调用函数并指定 this 值（thisArg），但参数以数组形式传递

```js
function sayHello(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}
const person = { name: "Bob" };
sayHello.apply(person, ["Hello", "!"]); // 输出: Hello, Bob!
```

- `function.bind(thisArg, arg1, arg2, ...)`

创建一个新函数，并永久绑定指定的 this 值（thisArg）和部分参数，但不会立即执行

```js
function sayHello(greeting) {
  console.log(`${greeting}, ${this.name}`);
}
const person = { name: "Dave" };
const boundFunc = sayHello.bind(person, "Hey");
boundFunc(); // 输出: Hey, Dave
```
