# CSS

Cascading Style Sheet，层叠样式表，为 HTML 网页添加样式

由选择器和声明组成，声明由属性和值组成，声明以分号结尾，放在花括号中

```css
selector {
    property1: value;
    property2: value;
}
```

样式优先级

- 内联样式 > 内部样式 > 外部样式 > 浏览器自定义/插件样式 > 浏览器默认样式
- ID选择器 > 类/伪类/属性选择器 > 元素/伪元素选择器
- 优先级相同时，后出现的会覆盖先出现的
- 添加了 `!important` 的样式具有最高优先级

## 引入方式

### 内联样式

直接在元素的标签中使用 `style` 属性添加样式

```html
<html>
    <head>
        ...
    </head>
    <body>
        <p style="color: red; font-size: 20px;"> 
            hello
        </p>
    </body>
<html>
```

### 内部样式

代码写在 `<style>` 标签中

```html
<html>
    <head>
        <title> demo </title>
        <style type="text/css">
            p strong {
                color: green;
                background-color: #d0f0f6;
                padding: 10px;
            }
        </style>    
    </head>
    <body>
        ...
    </body>
<html>
```

### 外部样式

代码写在单独的 `.css` 文件中，使用 `<link>` 标签引入

```html
<html>
    <head>
        <link rel="stylesheet" href="xxx.css" />
    </head>
    <body>
        ...
    </body>
<html>
```

## 选择器

### 基本选择器

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

### 分组选择器

```css
/* 同时匹配A,B选择器 */
div, span {
    ...
}
```

### 组合选择器

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

### 伪选择器

- 伪类

<https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes>

```css
/* 匹配选择具有焦点的 <input> 元素 */
input:focus {
    ...
}
```

- 伪元素

<https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-elements>

```css
/* 匹配所有 <p> 元素的第一行 */
p::first-line {
    ...
}
```

## display 属性

`display` 属性非常强大且灵活，允许开发者精确控制元素及其子元素的布局和表现形式

块级元素的默认值 `display: block`，行内元素默认值是 `display: inline`，列表等元素的默认值是 `display: list-item`

```text
block: 元素显示为块级元素，占据全宽，即使后面有内容也会换行显示。
inline: 元素显示为内联元素，不会导致文本换行。
flex: 元素将变为弹性容器，其直接子元素的布局将受到 Flexbox 布局的影响。
grid: 元素将变为网格容器，其直接子元素的布局将受到 CSS 网格布局的影响。
list-item: 元素会被显示为列表项，类似于 <li> 元素。
table、table-row、table-cell 等: 这些值将元素的行为变得类似于 HTML 表格中的 <table>、<tr>、<td> 等元素。

run-in: 根据上下文决定该元素是块级还是内联。
inline-block: 元素水平排列，类似于inline，但是可以设置宽度和高度。
inline-flex: 元素将变为内联的弹性容器。
inline-grid: 元素将变为内联的网格容器。
-webkit-flex: Webkit 内核浏览器，比如 Safari，必须加上 -webkit- 前缀

none: 元素不会被显示，并从文档流中消失。
contents: 元素本身不会被显示，但其子元素会像平常一样显示。
```

## 盒模型

![20240229151147](https://image.zuoright.com/20240229151147.png)

默认情况下，使用标准盒模型时，用 `width` 和 `height` 属性设置 `Content` 区域的宽和高，不包含边距和边框

但在替代盒模型（也叫 IE 或怪异盒模型）中，设置的是整个盒子的宽高，即包含内边距和边框。

```css
xxx {
    box-sizing: content-box;  /* 使用标准盒模型（默认） */
}

yyy {
    box-sizing: border-box;  /* 使用替代盒模型 */
}
```

- CSS Reset

不同的浏览器会给页面元素预设不同的外边距和内边距，这可能会导致在不同浏览器中网页呈现出不一致的布局，为了提高跨浏览器的一致性，通过设置所有元素的 `margin` 和 `padding` 为 0，然后根据需要为特定元素添加样式。

```css
* {
    margin: 0;
    padding: 0;
}
```

### 边框

`border` 包裹着内边距和内容区域，可以设置：

- 宽度
- 样式
    - 实线 solid
    - 圆点 dotted
    - 虚线 dashed
- 颜色

```css
div {
    border: 3px solid red;

    /* 等价于 */
    border-width: 3px;
    border-style: solid;
    border-color: red;
}
```

- 圆角

```css
.radius {
    border: 3px solid blue;

    border-radius: 20px;
    border-radius: 50%;
    border-top-right-radius: 10px;
}
```

![20240229190444](https://image.zuoright.com/20240229190444.png)

### 外边距

`margin` 是边框到其他元素的透明空间

可取值

- length 取值为负时元素会比原来更接近临近元素
- percentage
- auto 让浏览器自己选择一个合适的外边距，常用于居中

如果两个元素的外边距重叠了，则合并为一个外边距，取边距较大的值，比如某个元素的上边距 50px，其上方元素下边距 30px，则合并为一个 50px 的外边距

```css
div {
    /* 全局值 */
    margin: inherit;
    margin: initial;
    margin: unset;

    /* 应用于所有边距 */
    margin: 10px;
    margin: 1em;
    margin: -3px;

    /* 上下边 左右边 */
    margin: 5% auto;

    /* 上边 左右边 下边 */
    margin: 1em auto 2em;

    /* 上边 右边 下边 左边 */
    margin: 2px 1em 0 auto;
}

div {
    margin-top: 10px;
    margin-right: 20px;
    margin-bottom: 10px;
    margin-left: 20px;
}
```

### 内边距

`Padding` 指的是内容和边框之间的透明空间

可取值

- length 以固定值为内边距，不可以为负值，比如：1px
- percentage 相对于包含块的宽度，以百分比值为内边距，比如：5%

```css
div {
    /* 全局值 */
    padding: inherit;
    padding: initial;
    padding: unset;

    /* 应用于四个方向的内边距 */
    padding: 1em;

    /* 上下边 左右边 */
    padding: 5% 10%;

    /* 上边 左右边 下边 */
    padding: 1em 2em 2em;

    /* 顺时针：上边 右边 下边 左边 */
    padding: 5px 1em 0 2em;
}
```

### 内容溢出

内容溢出虽然不会影响到内外边距本身的大小或位置，但溢出的内容可以覆盖相邻元素的内容或外边距区域，使用 `overflow` 属性可以控制元素溢出的方式，其只工作于指定高度的块元素上

- `overflow: visible;`：默认值，内容溢出会显示在元素框之外
- `overflow: hidden;`：内容溢出部分不可见
- `overflow: scroll;`：内容溢出显示滚动条，不溢出也显示
- `overflow: auto;`：内容溢出显示滚动条，不溢出不显示

## 背景相关

同时设置多个背景属性，推荐顺序：`background: color image repeat attachment position/size`

- `background-color`

背景色作用于内容区和内边距

```css
/* 颜色值有三种定义方式 */
div {
    background-color: green;  /* 颜色名称 */
    background-color: #fff;  /* 十六进制 */
    background-color: rgb(255, 0, 0);  /* RGB */
}
```

- `background-image`

设置背景图

```css
.bgi {
    /* 使用url函数指向图像文件 */
    background-image: url(./img/demo.png);
}

.gradient {
    /* 渐变 */
    background-image: linear-gradient(90deg, rgba(119,0,255,1) 39%, rgba(0,212,255,1) 100%);
}
```

- `background-repeat`

控制背景图的平铺方式

```css
.bgr1 {
    background-repeat: repeat;  /* 水平和垂直方向都重复（默认） */
}

.bgr2 {
    background-repeat: repeat-x;  /* 水平方向重复 */
}

.bgr3 {
    background-repeat: repeat-y;  /* 垂直方向重复 */
}

.bgr4 {
    background-repeat: no-repeat;  /* 仅平铺一次 */
}
```

- background-size

```css
.bgs1 {
    background-size: auto;  /* 按图像比例尽可能放大，未覆盖部分重复平铺 */
}

.bgs2 {
    background-size: contain;  /* 按图像比例尽可能放大，未覆盖部分显示背景色 */
}

.bgs3 {
    background-size: cover;  /* 按图像比例一直放大，直到覆盖整个区域，超出部分裁剪 */
}
```

- background-position

设置背景图起始位置，表示一组2D坐标，默认左上角`（0, 0）`

也可以用长度、百分比、关键字等设置，还可以混用

关键字：`left|right|top|bottom|center`，表示将元素与2D框的指定边对齐，如果只设置一个轴的关键字，另一个轴则默认为 center

```css
    background-position: top center;
    background-position: 20px 10%;
    background-position: top 20px;
```

- background-attachment

```css
.bga1 {
    background-attachment: scroll;  /* 背景图随网页滚动而移动（默认） */
}

.bga2 {
    background-attachment: fixed;  /* 背景图固定不动 */
}

.bga3 {
    background-attachment: local;  /* 背景图随元素内容滚动 */
}
```

## CSS 函数

```css
/* RGB颜色 */
background-color: rgb(255, 0, 0);

/* 从文件返回图像 */
background-image: url(./img/wtflogo.png);

/* 动态计算 */
padding-right: calc(100vw - 100%);
/* 100vw是视口宽度，100%是内容宽度，那100vw - 100%就是滚动条宽度了，设置padding-right 为滚动条的宽度，解决滚动条隐藏和出现时导致的网页抖动问题 */
```

## 预处理器

CSS 预处理器是一个能让你处理自己独有的语法来生成 CSS 标准语法的程序，绝大多数 CSS 预处理器会增加一些原生 CSS 不具备的特性。

常见的有：Less、Stylus、Sass 等

## 参考

- <https://developer.mozilla.org/zh-CN/docs/Web/CSS>
- <https://www.wtf.academy/docs/css-101/HelloCSS/>
