# HTTP 请求库

AJAX, Asynchronous JavaScript and XML，由 Jesse James Garrett 在 2005 年提出，标志着 Web 2.0 的开端

AJAX 并不是一种具体的工具或 API，而是指在无需刷新整个页面的情况下，与服务器异步交换数据并更新部分网页的技术

## XHR

XHR, XMLHttpRequest 是浏览器提供的最早、最广泛使用的实现 AJAX 的工具。

由微软在 1999 年引入（IE5 中的 ActiveXObject），随着 AJAX 的流行被其他浏览器标准化为 XMLHttpRequest

XHR 提供了发起 HTTP 请求的能力，支持同步和异步通信，但现代应用中几乎总是使用异步模式。

### GET

```js
// 创建一个函数来发送 GET 请求
function fetchData() {
    // 1. 创建 XMLHttpRequest 对象
    const xhr = new XMLHttpRequest();

    // 2. 配置请求：方法、URL、是否异步
    xhr.open("GET", "https://jsonplaceholder.typicode.com/posts/1", true);

    // 3. 设置响应处理函数
    xhr.onreadystatechange = function () {
        // 检查请求状态
        if (xhr.readyState === 4) { // 4 表示请求已完成
        if (xhr.status === 200) { // 200 表示成功
            // 解析 JSON 数据并打印
            const response = JSON.parse(xhr.responseText);
            console.log("成功获取数据:", response);
        } else {
            // 处理错误
            console.error("请求失败，状态码:", xhr.status);
        }
        }
    };

    // 4. 发送请求
    xhr.send();
}

// 调用函数
fetchData();
```

### POST

```js
    // 创建一个函数来发送 POST 请求
function sendData() {
    const xhr = new XMLHttpRequest();

    // 1. 配置请求
    xhr.open("POST", "https://jsonplaceholder.typicode.com/posts", true);

    // 2. 设置请求头（如果是 JSON 数据）
    xhr.setRequestHeader("Content-Type", "application/json");

    // 3. 设置响应处理函数
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
        if (xhr.status === 201) { // 201 表示创建成功
            const response = JSON.parse(xhr.responseText);
            console.log("数据提交成功:", response);
        } else {
            console.error("请求失败，状态码:", xhr.status);
        }
        }
    };

    // 4. 准备要发送的数据
    const data = {
        title: "Hello World",
        body: "This is a test post",
        userId: 1,
    };

    // 5. 发送请求（将数据转为 JSON 字符串）
    xhr.send(JSON.stringify(data));
}

// 调用函数
sendData();
```

## JQuery

在 AJAX 技术流行后，开发者发现原生 XHR 使用起来繁琐且容易出错，jQuery 通过更高层次的抽象让 AJAX 请求变得更简单。除此之外还简化了 DOM 操作、事件处理、动画等浏览器相关的任务。

通过对原生 XMLHttpRequest 封装，提供了简化的 AJAX 方法，如：`$.ajax()`、`$.get()`、`$.post()`

原生 XHR 需要手动处理 `onreadystatechange` 等状态，而 jQuery 使用回调函数（后来支持 Promise）简化异步操作，让代码更直观

> 许多现代框架和库（如 React, Angular, Vue）也提供了自己的方式来处理 AJAX 请求

```js
// 通用方法
$.ajax({
    url: "https://jsonplaceholder.typicode.com/posts/1",
    method: "GET",
    success: function(data) {
        console.log("成功:", data);
        $("#result").text(data.title); // 更新 DOM
    },
    error: function(xhr, status, error) {
        console.error("失败:", error);
    }
});

// GET 请求
$.get("someurl", function(data) {
    console.log(data);
});
```

## Fetch API

ES6 引入的现代 API，用于替代 XHR 从服务器获取资源，提供了更简洁的接口和 Promise 支持。

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

Axios 是一个基于 Promise 的第三方 HTTP 库，最初是为了简化 XHR 的使用（在浏览器中基于 XHR，在 Node.js 中基于 http 模块）。

相比于 Fetch 有更简洁的 API 和默认行为，比如自动将响应数据解析为 JSON，内置请求和响应拦截器，可以在发送请求前或接收响应后统一处理逻辑，内置超时配置和取消功能，HTTP 错误会直接触发 catch，无需手动检查状态码，客户端支持防御 XSRF 等

许多现代前端框架和库（如 React）都默认或推荐使用 Axios 来处理 HTTP 请求

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

## 跨域

Form 表单发起的简单请求通常不涉及跨域，而 AJAX 请求默认受到严格的同源策略限制

同源策略是浏览器的一种安全机制，限制了不同源（协议、域名、端口）的资源之间的交互

> 服务器不涉及跨域，只有浏览器才涉及跨域，发请求可以正常发，但返回的时候会被拦截

- 同源：协议（如 http）、域名（如 example.com）、端口（如 80）完全相同。
    - http 和 https 属于不同的协议
    - 主域名和子域名都要相同
    - localhost 和 127.0.0.1 属于非同源
- 跨域：如果请求的目标地址与当前页面的源不同，则属于跨域请求

如果请求的目标地址是跨域的，浏览器会检查是否符合跨域资源共享（CORS）规则

```text
简单请求（如 GET、POST，且满足特定条件）：
    浏览器会发送请求，但要求服务器返回的响应头包含 Access-Control-Allow-Origin，允许当前源访问。
    如果服务器未设置正确的 CORS 头，浏览器会阻止 JavaScript 读取响应数据。
复杂请求（如 PUT、DELETE，或自定义头）：
    浏览器会先发送一个 OPTIONS 预检请求，询问服务器是否允许跨域。
    如果服务器不允许，实际请求不会发出。
```

如何解决跨域

- JSONP 绕开跨域
- 后端返回 CORS 请求头允许跨域
- 代理

浏览器发送请求给同源的代理服务器，代理服务器请求非同源的目标服务器（服务器之间为 HTTP 库请求，不涉及跨域），然后代理将请求返回给浏览器

实际项目中一般通过 Nginx 配置代理，React 等框架中也可以配置代理

- 方法1，在 package.json 中添加如下配置

只能配置一个服务器

```json
{
    "proxy": "http://localhost:5000"  // 5000 是目标服务器端口
}
```

- 方法2，在 `src/setupProxy.js` 中配置代理规则

可以配置多个服务器

```js
const proxy = require('http-proxy-middleware');

module.exports = function(app) {
    app.use(
        '/api1',  // 前端发送请求时，只有包含指定前缀的请求地址，才会转发
        proxy({
            target: 'http://localhost:5000',  // 目标服务器（转发给谁）----必须写
            changeOrigin: true,  // 控制服务器接收到的请求头中的 host 字段
            pathRewrite:{'^/api1': ''}  //去掉请求前缀(重写路径)---必须写
        })
    );
        app.use(
        '/api2',
        proxy({
            target: 'http://localhost:5001',
            changeOrigin: true,
            pathRewrite:{'^/api2': ''}
        })
    );
};
```
