# 事件

事件三要素

- 事件类型，由浏览器预定义的行为，比如：click、mouseover、keydown 等
- 事件源，触发事件的元素，比如：按钮、链接、输入框等
- 事件处理程序，响应事件的函数，在事件发生时被调用

## 元素属性绑定

不推荐，不安全且代码难以维护

```html
<button id="myButton" onclick="alert('Hello!')">Click Me!</button>

<script src="./test.js"></script>
```

## DOM 属性绑定

```js
// DOM属性绑定事件，按钮被点击时触发，弹出警告框
const btn = document.getElementById('myButton');
btn.onclick = function() {
    alert('你点击了按钮!');
};
```

## 事件监听器（推荐）

```js
const btn = document.getElementById('myButton');
btn.addEventListener(
  'click',
  function(event) {
    console.log('事件类型：', event.type)
    console.log('目标元素：', event.target)
    console.log(`鼠标点击位置 (${event.clientX}, ${event.clientY})`)
    alert('Hello!');
  },
  false  // 可选参数，指定事件处理程序是在捕获阶段（true）还是冒泡阶段执行（false，默认）
);
```

### 事件监听

```js
const btn = document.getElementById('myButton');
const sayHello = function() {
    alert('Hello!');
};

// 添加事件监听器
btn.addEventListener('click', sayHello, false);

// 移除事件监听器，防止内存泄漏
btn.removeEventListener('click', sayHello, false);
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
