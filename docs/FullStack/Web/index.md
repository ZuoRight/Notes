# 引言

## HTML

Hypertext Markup Language，超文本标记语言，用于定义网页的结构和内容

> [关于标记语言](./markup_languages.md)

HTML 由元素组成，元素由开始标签、内容和结束标签组成，元素可以有属性

`<head>` 头部的内容不会显示在网页中，可包含页面描述、CSS 样式表、字符编码声明等，省略时览器会假设存在一个隐含的头部

`<body>` 元素包含了网页所有可见的内容，省略时浏览器仍然会渲染那些位于 head 元素之后的内容，就好像它们被包含在一个隐含的 body 标签中一样。

虽然省略 head 或 body 元素时浏览器会尽力正确渲染内容，但不推荐

```html
<!DOCTYPE html>  <!-- 声明文档类型，HTML5 只需要如此简洁的表示  -->
<html lang="zh-CN">  <!-- lang 指定文档的语言 -->
    <head>
        <!-- 页面标题或收藏描述 -->
        <title> demo </title>

        <!-- 面向搜索引擎的元信息 -->
        <meta charset="utf-8">
        <meta name="description" content="页面描述">
        <meta name="keywords" content="关 键 字">
        <meta property="og:type" content="website">

        <!-- favicon像素大小为16*16，所使用的颜色不得超过16色 -->
        <!-- 标题栏icon -->
        <link rel="icon" href="favicon.ico" type="image/x-icon">
        <!-- 收藏夹icon -->
        <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
    </head>

    <body>
        <p>Hello <strong>HTML</strong>.</p>
        <a href="https://www.google.com">This is a link</a>
    </body>
</html>
```

### 空元素

不需要内容，也不需要结束标签

```html
<!-- 换行 Line Break -->
<br>

<!-- 水平分割线 Horizontal Rule -->
<hr>

<!-- 图片 -->
<img src="image.jpg" alt="My Image">

<!-- 输入框 -->
<input type="email" id="email" name="email" placeholder="请输入...">
```

HTML5 旨在简化网页的编码，并增强网页的兼容性和灵活性，所以在 HTML5 中空元素不需要闭合斜杠。

而 XML 以及 XHTML 等要求所有元素都必须被正确地闭合：`<br />`, `<hr />`

### 元素属性

元素属性定义在开始标签中，用于提供有关元素的更多信息，或者定义某些行为

格式：`<div name1="value1" name2="value2">`

- `name`
- `id`
- `class`
- `style`
- `src` Source，用于嵌入资源，如图像、脚本、iframe，当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕。常见于 `<img>`, `<script>`, `<iframe>`, `<video>`, `<audio>` 等标签中
- `href` Hypertext Reference，用于链接资源，会下载资源但不会停止对当前文档的处理，如网页、样式表、锚点，常见于 `<a>`, `<link>` 等标签中

## CSS

Cascading Style Sheet，层叠样式表，为 HTML 网页添加样式

由选择器和声明组成，声明由属性和值组成，声明以分号结尾，放在花括号中

```css
selector {
    property1: value;
    property2: value;
}
```

### 样式优先级

- 内联样式 > 内部样式 > 外部样式 > 浏览器自定义/插件样式 > 浏览器默认样式
- ID 选择器 > 类/伪类/属性选择器 > 元素/伪元素选择器
- 添加了 `!important` 的样式具有最高优先级
- 优先级相同时，后出现的会覆盖先出现的

### 引入方式

```html
<html>
    <head>
        <!-- 内部样式 -->
        <style> * {margin: 0; padding: 0} </style>

        <!-- 引入样式，代码写在单独的 .css 文件中 -->
        <link rel="stylesheet" type="text/css" href="demo.css">
    </head>
    <body>
        <!-- 内联样式，直接在元素的标签中使用 style 属性添加 -->
        <p style="color: red; font-size: 20px;"> hello </p>
    </body>
<html>
```

### 选择器

- 基本选择器

```css
/* 通用选择器 */
* {
    ...
}

/* 元素选择器 */
element_name {
    ...
}

/* 类选择器 */
.class_name {
    ...
}

/* id选择器 */
#id_name {
    ...
}

/* 属性选择器 */
[property_name="value"] {
    ...
}
```

- 分组选择器

```css
/* 同时匹配A,B选择器 */
div, span {
    ...
}
```

- 组合选择器

```css
/* 匹配所有位于任意 <div> 元素之内的 <span> 元素 */
div span {
    ...
}

/* 匹配直接嵌套在 <ul> 元素内的所有 <li> 元素 */
ul > li {
    ...
}

/* 匹配同一父元素下，<p> 元素后的所有 <span> 元素 */
p ~ span {
    ...
}

/* 匹配所有紧邻在 <p> 元素后的 <span> 元素 */
p + span {

}
```

- 伪选择器

[伪类](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes)

```css
/* 匹配选择具有焦点的 <input> 元素 */
input:focus {
    ...
}
```

[伪元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-elements)

```css
/* 匹配所有 <p> 元素的第一行 */
p::first-line {
    ...
}
```

### 函数

```css
/* 从文件返回图像 */
background-image: url(./img/wtflogo.png);

/* RGB颜色 */
background-color: rgb(255, 0, 0);

/* 动态计算宽度 */
padding-right: calc(100vw - 100%);
/* 100vw 是视口宽度，100% 是内容宽度，那 100vw - 100% 就是滚动条宽度了，设置 padding-right 为滚动条的宽度，解决滚动条隐藏和出现时导致的网页抖动问题 */
```

### 预处理器

常见的有：Less、Stylus、Sass 等

可以让你处理自己独有的语法来生成 CSS 标准语法的程序，绝大多数 CSS 预处理器会增加一些原生 CSS 不具备的特性。

## JS 脚本

在 HTML 中，可以使用 `<script>` 标签引入 JavaScript 代码。可以放在 HTML 文档的 `<head>` 或 `<body>` 部分，但为了避免阻塞页面的渲染，通常会放在 `<body>` 标签的末尾。

```html
<body>
    ......
    
    <!-- 内部脚本 -->
    <script> alert('Hi!'); </script>

    <!-- 嵌入脚本 -->
    <script src="/path/to/script.js"></script>
</body>
```

## 参考

- <https://www.bilibili.com/video/BV1Wr4y1R7Bd>

---

- <https://wangdoc.com/html/intro.html>
- <https://www.wtf.academy/docs/html-101/HelloHTML/>
- <https://developer.mozilla.org/zh-CN/docs/Learn/HTML>

---

- <https://www.wtf.academy/docs/css-101/HelloCSS/>
- <https://developer.mozilla.org/zh-CN/docs/Web/CSS>
