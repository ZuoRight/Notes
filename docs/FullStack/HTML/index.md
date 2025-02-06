# 引言

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

        <!-- 内部样式 -->
        <style> * {margin: 0; padding: 0} </style>
        <!-- 引入样式 -->
        <link rel="stylesheet" type="text/css" href="demo.css">
    </head>

    <body>
        <p>Hello <strong>HTML</strong>.</p>
        <a href="https://www.google.com">This is a link</a>
    </body>
</html>
```

在 HTML 中，可以使用 `<script>` 标签插入 JavaScript 代码。可以放在 HTML 文档的 `<head>` 或 `<body>` 部分，但为了避免阻塞页面的渲染，通常会放在 `<body>` 标签的末尾。

```html
<body>
    ......
    
    <!-- 内部脚本 -->
    <script> alert('Hi!'); </script>

    <!-- 嵌入脚本 -->
    <script src="/path/to/script.js"></script>
</body>
```

## 元素属性

元素属性定义在开始标签中，用于提供有关元素的更多信息，或者定义某些行为

格式：`<div name1="value1" name2="value2">`

- `name`
- `id`
- `class`
- `style`
- `src` Source，用于嵌入资源，如图像、脚本、iframe，常见于 `<img>`, `<script>`, `<iframe>`, `<video>`, `<audio>` 等标签中
- `href` Hypertext Reference，用于链接资源，如网页、样式表、锚点，常见于 `<a>`, `<link>` 等标签中

## 元素分类

### 区块元素

block-level elements，比如：`<div>`, `<p>`, `<ul>`, `<h1> ~ <h6>` 等

以块的形式在页面中独占一行，与其他区块元素上下堆叠，可以设置宽高，默认为父容器的 100%

### 行内元素

inline elements，比如：`<img>`, `<input>`, `<span>`, `<strong>` 等

在文本中流动，不会开始新的一行，不可设置宽高，仅占据它们包含的内容所需要的宽度

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

## 语义化

在 HTML5 中，引入了一系列新的语义华标签，可以更清晰地描述内容和页面结构，使代码更易读易懂，方便屏幕阅读器和移动设备等解析，同时也有助于搜索引擎优化 (SEO)

- 全用 div
![20240304132749](https://image.zuoright.com/20240304132749.png)

- 语义化

![20240304132758](https://image.zuoright.com/20240304132758.png)

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML5 语义化标签示例</title>
</head>
<body>
    <header>
        <h1>网站名称</h1>
        <nav>
            <ul>
                <li><a href="#">首页</a></li>
                <li><a href="#">关于我们</a></li>
                <li><a href="#">产品服务</a></li>
                <li><a href="#">联系方式</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <article>
            <header>
                <h2>文章标题</h2>
                <p>发布日期：2024-03-04</p>
            </header>
            <section>
                <h3>章节标题</h3>
                <p>这里是章节内容。</p>
            </section>
            <section>
                <h3>另一个章节标题</h3>
                <p>这里是另一个章节的内容。</p>
            </section>
            <footer>
                <p>作者：张三</p>
            </footer>
        </article>

        <aside>
            <h4>侧边栏标题</h4>
            <p>这里是侧边栏的内容，通常包括链接、广告或其他信息。</p>
        </aside>
    </main>

    <footer>
        <p>版权所有 © 2024 网站名称</p>
        <address>
            联系我们：<a href="mailto:info@example.com">info@example.com</a><br>
            电话：<a href="tel:+1234567890">+123-456-7890</a>
        </address>
    </footer>
</body>
</html>
```

## 参考

- <https://wangdoc.com/html/intro.html>
- <https://www.wtf.academy/docs/html-101/HelloHTML/>
- <https://developer.mozilla.org/zh-CN/docs/Learn/HTML>
