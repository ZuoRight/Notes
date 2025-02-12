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

## 语句

### 条件语句

- `if () {} else {}`

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

- 简写：三元运算符

```js
const r = x > y ? x : y;  // 如果 x>y 则 r=x，否则 r=y
```

- `switch () {}`

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

### 循环语句

- `for (声明循环变量赋初始值; 循环条件; 增量表达式) {执行语句}`

```js
for (let i = 0; i < 3; i++) {
  console.log(`i 当前的值为：${i}`);
}
```

- `while (循环条件) {增量表达式}`

```js
let i = 0
while (i < 3) {
  console.log(`i 当前的值为：${i}`)
  i++
}
```

- `do {增量表达式} while (循环条件)`

无论条件是否为真，先执行一次语句，再做判断循环

```js
let i = 0
do {
  console.log(`i 当前的值为：${i}`);
  i++;
} while (i < 3);
```

### 跳转语句

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

- 扩展运算符

```js
function addNumbers(x, y, z) {
  return x + y + z;
}

const numbers = [1, 2, 3];

// 使用扩展运算符将数组中的元素展开作为函数的参数
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

> ES6 新增

```js
let sum = (x, y) => {
  return x + y;
}

// 只有一个 return 语句时，可不写 return，并省略掉花括号
let y = x => x * x;
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
alert(`You are ${age} years old!`); // 第一个模态窗：确定age=100，取消age=null
```
