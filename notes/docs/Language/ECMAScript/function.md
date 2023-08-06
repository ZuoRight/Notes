# JS 函数

```js
function sum(x=0, y=0) {
    return x + y;
}

sum(1, 2);  // 3
sum(undefined, 2)  // 2，x使用默认值
sum(1)  // 1，y使用默认值
```

- 解构式传参

```js
function sum({x=0, y=0}) {
    return x + y;
}

sum({x:1, y:2})  // 3
```

## 匿名函数

- 函数表达式

```js
let sum = function(x, y) {
    return x + y;
};  // 注意末尾要带分号，表示赋值语句结束

data: function () { pass } 可简写为 data () { pass }
```

- 箭头函数

```js
// ES6 新增
let sum = (x, y) => {
    return x + y;
}
```

## 回调函数

函数作为参数，传给另一个函数时被称之为回调函数，早期用于异步

```js
function callback() {
  console.log('Hello, JavaScript!');
}

setTimeout(callback, 1000);  // 定时器，1000毫秒后，执行callback
console.log('hello');

// hello
// Hello, JavaScript! (1 秒后输出)
```

## 闭包

函数嵌套，内部函数作为外部函数的返回值

示例：统计 demo 函数被调用次数

- 非闭包实现

```js
let count = 0;  // 这里的count是全局变量，有可能会被其它函数修改

function demo() {
  count++;
  return count;
}

demo();  // 1
demo();  // 2
```

- 闭包实现，防止变量污染

```js
function demo() {
  let count = 0;  // 这里的count是局部变量，不会被其它函数修改

  return function () {
    count++;
    return count;
  };
}

let addCount = demo();

addCount(); // 1
addCount(); // 2
```

## 异步函数

### Promise

ES6 引入了 Promise 用于替代回调函数来实现异步，类似 Python 的 Asyncio

Promise 对象有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）

```js
// 定义 promise 实例
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Hello, JavaScript Promise!');
  }, 1000)
})

// 运行 Promise 实例
promise.then((value) => {
  console.log(value);
})

console.log('hello Promise');

// hello Promise
// Hello, JavaScript Promise! (1 秒后输出)
```

### async/await

async/await 是 Promise 的语法糖，让异步编程更易于理解和使用

在一个函数前面加上 async 关键字 ，将它变为异步函数，它的返回值会被自动包装为一个 Promise

await 关键字只能在 async 函数内工作，后接 Promise 对象，等待 Promise 变成 fulfilled 或 rejected

- 示例1

```js
async function helloAwait() {
  const value = await hello();
  console.log(value);
  console.log('hello await');
}

helloAwait()
// Hello, JavaScript async! (1 秒后输出)
// hello await
```

- 示例2

```js
async function getBaycMetadata(){
  const url = `https://ipfs.io/ipfs/QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/1`;
  const response = await fetch(url);  // fetch() 用于HTTP请求，await 返回数据后才会继续执行下一步
  const BaycMetadata = await response.json();
  console.log(BaycMetadata);
}

getBaycMetadata()
// image: "ipfs://QmPbxeGcXhYQQNgsC6a36dDyYUcHgMLnGKnF8pVFmGsvqi"
// attributes...
```

## 内置函数

```js
alert('Hello World');

// 控制台输出log
console.log("hello %s", "7c")  // 格式化：%s 字符，%d 整数，%f 浮点数，%o 对象
```
