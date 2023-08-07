# JS 请求方式

## AJAX

Asynchronous JavaScript and XML，一种在无需刷新整个页面的情况下，与服务器异步交换数据并更新部分网页的技术。

主要依靠 `XMLHttpRequest` 对象

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

## Fetch API

- GET

```js
fetch('https://api.github.com/search/users?q=zuoright', {method: 'GET',})  // 发起Get请求
.then(response => response.json())  // 处理响应返回Json格式
.then(data => console.log(data))  // 打印返回数据
.catch((error) => console.error('Error:', error));  // 捕获错误
```

- POST

```js
fetch('https://api.example.com/data', {
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

Axios 是一个基于 Promise 的 HTTP 库

可以拦截请求和响应，转换请求和响应数据，取消请求，自动转换 JSON 数据，客户端支持防御 XSRF 等。

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
