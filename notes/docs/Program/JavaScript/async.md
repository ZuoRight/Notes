# 异步编程

在 JS 引入 Promise 之前，异步编程主要依赖于「回调函数」。回调函数是一种将函数作为参数传递给另一个函数，并在异步操作完成后执行的机制。当异步操作嵌套多层时，代码变得难以阅读和维护，这种现象被称为「回调地狱」

```js
function doSomethingAsync(callback) {
    // 模拟异步操作，例如网络请求或文件读写
    setTimeout(() => {
        const result = 'Result of some asynchronous operation';
        callback(result); // 异步操作完成后调用回调函数
    }, 1000);
}

function handleResult(result) {
    console.log('Got result:', result);

    // 这里可以继续其他的异步操作，也需要传入回调函数
    doAnotherThingAsync(callback);
}

doSomethingAsync(handleResult);
// Result of some asynchronous operation
// Got result:
```

为了解决回调地狱（callback hell）问题，ES6 引入了 Promise 链式调用，它提供了一种更加优雅的处理异步操作的方法

Promise 对象表示一个可能还没有完成的操作的最终结果，有三种状态：pending（进行中）、fulfilled（已成功）、rejected（已失败）

```js
function doSomethingAsync() {
  return new Promise((resolve, reject) => {
    // 设置定时器模拟异步操作，例如网络请求或文件读写
    setTimeout(() => {
        // 假设异步操作成功，并传递结果
        resolve('Result of the asynchronous operation');
        // 如果异步操作失败，可以使用 reject 回调来拒绝 Promise
        reject('Error message');
    }, 1000);
  });
}

// .then 用于处理成功的操作，会返回一个新的 Promise，继续链式调用
// .catch 用来捕获和处理错误，返回的依然是 Promise
// 最后还可以接 .finally，无论 Promise 的状态如何都会执行
doSomethingAsync()
    .then(result => {
        console.log('Success:', result);
        return doAnotherThingAsync(result);
    })
    .then(result => {
        console.log('Another success:', result);
    })
    .catch(error => {
        console.error('Error:', error);
    })
    .finally(() => {
        console.log('finally')
    });
```

## async/await

`async/await` 是 Promise 的语法糖，避免了复杂的回调和链式调用，让异步编程代码更加简洁，易于理解和使用。

在一个函数前面加上 `async` 关键字 ，将它变为异步函数，它的返回值会被自动包装为一个 Promise，将 `.then` 和 `.catch` 链式调用转换为更加直观的 `try/catch` 块，同时使用 `await` 等待 Promise 的解决。

```js
async function doSomethingAsync() {
  return new Promise((resolve, reject) => {
    // 模拟异步操作
    setTimeout(() => {
      const result = 'Result of the asynchronous operation';
      resolve(result);
      // 如果需要模拟错误，可以取消下面一行的注释
      // reject('Error message');
    }, 1000);
  });
}

// 假设还有另一个异步函数
async function doAnotherThingAsync(result) {
  // 返回一个基于结果的新 Promise
  return `Processed ${result}`;
}

// 使用 async/await 语法
async function handleAsyncTasks() {
  try {
    const result = await doSomethingAsync();
    console.log('Success:', result);

    const anotherResult = await doAnotherThingAsync(result);
    console.log('Another success:', anotherResult);
  } catch (error) {
    // 捕获并处理异步操作中发生的错误
    console.error('Error:', error);
  }
}

// 调用异步函数
handleAsyncTasks();
```

## Event Loop

<https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7>

JS 引擎只执行同步任务，异步任务会有工作线程来执行，当需要进行异步操作，主线程会发一个异步任务的请求，相应的工作线程接受请求。当工作线程完成工作之后，通知主线程，主线程接收到通知之后，会执行一定的操作（回调函数）。

主线程和工作线程之间的通知机制就叫做事件循环。

![20240412125540](https://image.zuoright.com/20240412125540.png)

事件循环可视化：<https://www.jsv9000.app>

在 JavaScript 的事件循环机制中，任务被分为两种类型：

- 宏任务（Macrotask），又叫 Task，由 JavaScript 引擎线程直接执行的任务，即上图任务队列中的任务，包括
    - 整个脚本（main script）
    - setTimeout 和 setInterval 的回调
    - setImmediate（Node.js环境）等
- 微任务（Microtask），又叫 Jobs，可以看成是插队需要及时处理的任务，是在当前宏任务结束后立即执行的任务，包括
    - Promise 的 then 和 catch 的回调
    - process.nextTick（Node.js环境）
    - MutationObserver的回调（浏览器环境）等

![20240412125642](https://image.zuoright.com/20240412125642.png)

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
