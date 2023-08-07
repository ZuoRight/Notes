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

- 函数表达式

```js
let sum = function(x, y) {
    return x + y;
};  // 注意末尾要带分号，表示赋值语句结束

data: function () { pass } 可简写为 data () { pass }
```

- 箭头函数

> ES6 新增

```js
let sum = (x, y) => {
    return x + y;
}

// 只有一个 return 语句时，可不写 return，并省略掉花括号
let y = x => x * x;
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

ES6 引入了 Promise 用于替代回调函数来实现异步，类似 Python 的 Asyncio

Promise 对象有三种状态：pending（进行中）、fulfilled（已成功）、rejected（已失败）

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

### Promise

Promise 等价于 Python 中的 Future

```js
// 使用构造函数创建一个 Promise
const promise = new Promise((resolve, reject) => {
    // 异步操作，比如：读取文件、网络请求等
    // 设置定时器模拟异步操作
    setTimeout(() => {
        // 成功后返回 resolve() 中的内容，失败可以调用 reject()
        resolve('Hello, JavaScript Promise!');
    }, 1000)
})

// 用 .then 管理异步代码
// 接受两个回调函数作为可选参数
promise.then(
    value => {
        // 处理解析值的回调函数
    },
    error => {
        // 处理错误的回调函数
    }
)  // .then 会返回一个新的 Promise，可以链式调用
.then(
    newValue => console.log(newValue)
)  // 还可以接catch来处理错误，.catch 返回的依然是 Promise
.catch(
    error => console.error(error)
)  // 最后还可以接 .finally，无论 Promise 的状态如何都会执行
.finally(
    () => {
        console.log('finally')
    }
);


console.log('hello Promise');

// hello Promise
// Hello, JavaScript Promise! (1 秒后输出)
```

- 链式 Promise

```js
fetch('https://api.example.com/data') // 返回一个 Promise
    .then(response => response.json()) // 返回一个新的 Promise
    .then(data => console.log(data)) // 返回一个新的 Promise
    .catch(error => console.error(error)); // 返回一个新的 Promise
```

- 并行 Promise

```js
Promise.all([
    fetch('https://api.example.com/data1'),
    fetch('https://api.example.com/data2')
])
    .then(([response1, response2]) => Promise.all([response1.json(), response2.json()]))
    .then(([data1, data2]) => {
        console.log('Data 1:', data1);
        console.log('Data 2:', data2);
    })
    .catch(error => console.error(error));
```

- Promise API

```text
Promise.reject(reason)：返回一个用给定的原因拒绝的 Promise。
Promise.resolve(value)：返回一个以给定值解析后的 Promise。如果该值是一个 Promise，返回的 Promise 将具有相同的状态和值。
Promise.race(iterable)：返回一个新的 Promise，它在 iterable 中的任何 Promise 解析或拒绝后具有相同的解析值或拒绝原因。
Promise.all(iterable)：返回一个新的 Promise，它在 iterable 中的所有 Promise 都解析后解析，或者在 iterable 中的任何 Promise 被拒绝后拒绝。
Promise.any: 与 Promise.race 类似，区别在于只要有一个 Promise 实例变成 fulfilled 状态，包装实例就会变成 fulfilled 状态；所有 Promise 实例都变成 rejected 状态，包装实例才会变成 rejected 状态。
Promise.allSettled(iterable): 有时我们希望等到一组异步操作都结束了，不管每一个操作是成功还是失败，再进行下一步操作。但是，Promise.all 方法只适合所有异步操作都成功的情况，如果有一个操作失败，就无法满足要求。这时我们需要使用 Promise.allSettled 方法。
```

### Event Loop

在 JavaScript 的事件循环机制中，任务被分为两种类型：

- 宏任务（Macrotask）：由 JavaScript 引擎线程直接执行的任务，包括
  > 整个脚本（main script）  
  > setTimeout 和 setInterval 的回调  
  > setImmediate（Node.js环境）等
- 微任务（Microtask）：微任务是在当前宏任务结束后立即执行的任务，包括
  > Promise 的 then 和 catch 的回调  
  > process.nextTick（Node.js环境）  
  > MutationObserver的回调（浏览器环境）等

执行步骤：

1. 从宏任务队列中取出一个任务来执行
2. 执行完这个任务后，执行所有的微任务
3. 当微任务队列清空后，进入下一次事件循环，执行下一个宏任务

```js
// 宏任务
console.log('script start');  // 微任务

// 宏任务
setTimeout(function() {
    console.log('setTimeout');  // 微任务
}, 0);

// 宏任务
Promise.resolve().then(function() {
    console.log('promise1');  // 微任务
}).then(function() {
    console.log('promise2');  // 微任务
});

// 宏任务
console.log('script end');  // 微任务

// 输出顺序
/*
script start
script end
promise1
promise2
setTimeout
*/
```
