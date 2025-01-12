# HTTP 请求库

基本都是异步的

## XHR

XMLHttpRequest 提供了在 JavaScript 中发起 HTTP 请求的能力，支持同步和异步通信，但现代应用中几乎总是使用异步模式。

## AJAX

Asynchronous JavaScript and XML，一种在无需刷新整个页面的情况下，与服务器异步交换数据并更新部分网页的技术，主要是对 XHR 的封装。

代码通常较为冗长，需要手动处理请求和响应。

```js
// 创建 XMLHttpRequest 对象
let xhr = new XMLHttpRequest();

// 指定请求的方法和 URL
xhr.open("GET", "https://api.github.com/search/users?q=zuoright", true);

// 设置回调函数，处理请求的响应
xhr.onreadystatechange = function () {
    // 请求成功
    if (xhr.readyState == 4 && xhr.status == 200) {
        // 处理响应数据
        console.log(JSON.parse(xhr.responseText));
    }
}

// 发送请求
xhr.send();
```

jQuery 提供了简化 AJAX 操作的方法，许多现代框架和库（如 React, Angular, Vue.js）也提供了自己的方式来处理 AJAX 请求

```js
$.get("someurl", function(data) {
    console.log(data);
});
```

## Fetch API

是现代浏览器提供的原生 JavaScript API，用于从服务器获取资源。它是 XMLHttpRequest 的现代替代品，提供了更简洁的接口和 Promise 支持。

- GET

```js
fetch(
    'https://api.github.com/search/users?q=zuoright',
    {
        method: 'GET',
    }
)
.then(response => response.json())  // 处理响应返回Json格式
.then(data => console.log(data))  // 打印返回数据
.catch((error) => console.error('Error:', error));  // 捕获错误
```

- POST

```js
fetch(
    'https://api.example.com/data',
    {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
    },
    body: JSON.stringify({username: '0xAA', password: 'pwd',}),
})
.then(response => response.json())
.then(data => console.log(data))
.catch((error) => console.error('Request failure:', error));
```

## Axios

Axios 是一个基于 Promise 的第三方 HTTP 库，可以拦截请求和响应，转换请求和响应数据，取消请求，自动转换 JSON 数据，客户端支持防御 XSRF 等。

许多现代前端框架和库（如 React）都推荐或默认使用 Axios 来处理 HTTP 请求

```js
const axios = require('axios');

axios.get('https://api.github.com/search/users?q=zuoright')
.then(function (response) {
    console.log(response.data);
})
.catch(function (error) {
    console.log(error);
});
```
