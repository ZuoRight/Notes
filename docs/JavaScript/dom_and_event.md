# 对象模型和事件

```html
<!-- 示例模版 -->
<body>
    <div id="myId"></div>
    <div class="myClass"></div>
    <div class="myClass"></div>

    <script src="./demo.js"></script>
</body>
```

## DOM

Document Object Model 文档对象模型，核心对象：`document.xxx`

用于访问和操作 HTML 或 XML文档的 API，它将文档表示为由节点和对象（元素、属性、文本）组成的树形结构

- 获取元素

```js
document.querySelector('#myClass')  // 根据属性选择第一个匹配的元素
document.querySelectorAll('.myClass')  // 根据属性选择所有匹配的元素

document.getElementsByTagName('div')  // 根据标签名选择所有匹配的元素
document.getElementById('myId')  // 根据id选择唯一匹配元素
document.getElementsByClassName('myClass') // 根据class选择所有匹配的元素
```

- 增加元素

```js
// 创建新节点
item = document.createElement('div')

// 将子节点添加到父节点的末尾
list.appendChild(item)  // 是一个较早的 DOM 方法，只能接受一个节点对象，返回被添加的节点
list.append(item);  // 是较新的 DOM 方法，可以接受节点对象或 DOMString（文本），没有返回值
// 将子节点添加到父节点的开头
list.prepend(item);
```

- 删除元素

```js
item.remove(); // 使用 remove 方法直接删除元素

list.removeChild(item); // 使用 removeChild 方法删除子节点
list.innerHTML = ''; // 设置 innerHTML 为空字符串，删除所有子元素

const emptyElement = document.createElement('div'); // 创建一个空元素
list.replaceChild(emptyElement, item); // 使用 replaceChild 方法替换元素
```

- 获取/修改元素内容

```js
xxx.textContent = 'Hello, JavaScript!'  // 修改文本内容

// 修改 HTML 内容
xxx.innerHTML = `
    <strong>Hello, JavaScript!</strong>
`
```

- 获取/修改元素样式

注意：CSS 属性名都需要转换为驼峰命名法

```js
xxx.style.color = 'red'  // 修改文本颜色
xxx.style.backgroundColor = 'black'  // 修改背景颜色
```

## BOM

Browser Object Model 浏览器对象模型，核心对象：`window.xxx`

提供了与浏览器窗口进行交互的方法和属性，比如可以弹出新的窗口，改变状态栏中的文本，操作 Cookie 等

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

## 事件

事件三要素

```text
事件源，触发事件的元素，比如：按钮、链接、输入框等
事件类型，由浏览器预定义的行为，比如：click, mouseover, keyup, keydown 等，也可以自定义事件
事件处理程序，响应事件的函数，在事件发生时被调用
```

- 元素属性绑定

> 不推荐，不安全且代码难以维护

```html
<button id="myButton" onclick="alert('Hello!')">Click Me!</button>

<script src="./test.js"></script>
```

- DOM 属性绑定

```js
// DOM属性绑定事件，按钮被点击时触发，弹出警告框
const btn = document.getElementById('myButton');
btn.onclick = function() {
    alert('你点击了按钮!');
};
```

- 事件监听器（推荐）

```js
const input = document.getElementById('#todo');
input.addEventListener(
    'keyup',
    // 按键名称、鼠标位置等所有用户动作相关信息都会被打包到事件对象中传递给函数，通常被命名为 e 或者 event
    function(e) {
        console.log('事件类型：', e.type)  // keyup
        console.log('目标元素：', e.target)  // <input type="text" id="todo">
        console.log(`鼠标点击位置 (${e.clientX}, ${e.clientY})`)  // (undefined, undefined)
        console.log(e.key);  // 用户输入的内容
        if (e.key === 'Enter') {
            console.log(input.value);  // 按下回车后打印输入的内容
        }
    },
    false  // 可选参数，false: 函数在事件冒泡阶段执行（默认），true: 捕获阶段触发
);


// 示例2
const btn = document.querySelector('#btn');
const sayHello = function() {
    alert('Hello!');
};
btn.addEventListener('click', sayHello);  // 添加事件监听器
btn.removeEventListener('click', sayHello);  // 移除事件监听器，防止内存泄漏
```

### 事件委托

事件有冒泡和捕获的特性，可以不直接监听实际的目标元素，而是监听其父元素

```js
const ul = document.querySelector('ul')

ul.addEventListener('click', function (event) {
    if (event.target.tagName.toLowerCase() === 'li') {
        alert(`内容为 ${event.target.textContent} 的 li 被点击了！`)
    }
})
```

### 自定义事件

```js
const customEvent = new CustomEvent('myEvent', {
  detail: { message: '这是一个自定义事件' },
})

document.addEventListener('myEvent', (event) => {
  console.log('自定义事件被触发了：', event.detail.message)
})

document.dispatchEvent(customEvent)  // 触发事件
```
