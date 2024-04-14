# 对象模型

## DOM

> 核心对象：`document.xxx`

Document Object Model 文档对象模型

用于访问和操作 HTML 或 XML文档的 API，它将文档表示为由节点和对象（元素、属性、文本）组成的树形结构

```html
<!-- 示例模版 -->
<body>
    <div id="myId"></div>
    <div class="myClass"></div>
    <div class="myClass"></div>
    <script src="./DOMAndBOM.js"></script>
</body>
```

- 获取元素

```js
const elsByTag = document.getElementsByTagName('div')  // 根据标签名选择所有匹配的元素
const elsByClass = document.getElementsByClassName('myClass') // 根据class选择所有匹配的元素
const elsBySelectorAll = document.querySelectorAll('.myClass')  // 根据属性选择所有匹配的元素
const elBySelector = document.querySelector('#myClass')  // 根据属性选择第一个匹配的元素
const elById = document.getElementById('myId')  // 根据id选择唯一匹配元素
```

- 增加/删除元素

```js
document.body.removeChild(elById) // 删除元素

const newEl = document.createElement('div')  // 创建新元素
document.body.appendChild(newEl)  // 添加元素
```

- 修改内容

```js
elById.textContent = 'Hello, JavaScript!'  // 修改文本内容
elById.innerHTML = '<strong>Hello, JavaScript!</strong>'  // 修改 HTML 内容
```

- 修改样式

> CSS 属性名都需要转换为驼峰命名法

```js
elById.style.color = 'red'  // 修改文本颜色
elById.style.backgroundColor = 'black'  // 修改背景颜色
```

## BOM

> 核心对象：`window.xxx`

Browser Object Model 浏览器对象模型

提供了与浏览器窗口进行交互的方法和属性，比如可以弹出新的窗口，改变状态栏中的文本，操作Cookie等

```js
window.open('https://zuoright.com', '_blank')  // 在新标签页打开URL

const width = window.innerWidth  // 获取窗口宽度
const height = window.innerHeight  // 获取窗口高度
console.log(`窗口宽度：${width}，窗口高度：${height}`)
```

- 设置定时器

```js
// 1 秒后输出 Hello!
setTimeout(() => {
  console.log('Hello!')
}, 1000)

// 每隔 1 秒输出一次 Hello!
setInterval(() => {
  console.log('Hello!')
}, 1000)
```
