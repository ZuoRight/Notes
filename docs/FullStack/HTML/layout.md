# 布局

浏览器默认的布局，各个元素完全按照源码出现的先后顺序显示。

- CSS Reset

不同的浏览器会给页面元素预设不同的外边距和内边距，这可能会导致在不同浏览器中网页呈现出不一致的布局，为了提高跨浏览器的一致性，通过设置所有元素的 `margin` 和 `padding` 为 0，然后根据需要为特定元素添加样式。

```css
* {
    margin: 0;
    padding: 0;
}
```

## 显示形式

`display: xxx` 允许开发者精确控制元素及其子元素的布局和表现形式

```text
block: 以块的形式独占一行，与其他区块元素上下堆叠，可以设置宽高，默认为父容器的 100%，比如：div, p, ul, h1 等
inline: 元素显示为行内形式，不可设置宽高，仅占据它们包含的内容所需要的宽度，比如：img, input, span, strong 等
list-item: 元素显示为列表项，比如 li
table 比如 table
    table-header-group 比如 thead
    table-footer-group 比如 tfoot
    table-row-group 比如 tbody
    table-row 比如 tr
    table-cell 比如 td

flex: 元素将变为弹性容器，其直接子元素的布局将受到 Flexbox 布局的影响。
grid: 元素将变为网格容器，其直接子元素的布局将受到 CSS 网格布局的影响。

run-in: 根据上下文决定该元素是块级还是内联。
inline-block: 元素水平排列，类似于inline，但是可以设置宽度和高度。
inline-flex: 元素将变为内联的弹性容器。
inline-grid: 元素将变为内联的网格容器。
-webkit-flex: Webkit 内核浏览器，比如 Safari，必须加上 -webkit- 前缀

none: 元素不会被显示，并从文档流中消失。
contents: 元素本身不会被显示，但其子元素会像平常一样显示。
```

```css
{
	text-align: center;  /* 设置块级元素的文本水平对齐方式，left（左对齐，默认）、right（右对齐）、center（居中对齐）、justify（两端对齐） */
	vertical-align: middle  /* 用于设置内联元素或表格单元格的垂直对齐方式，baseline（基线对齐）、top（顶部对齐）、middle（垂直居中对齐）、bottom（底部对齐，默认）、sub（下标对齐）、super（上标对齐）、text-top（与父元素的文本顶部对齐）、text-bottom（与父元素的文本底部对齐） */
}
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

### 边框

`border` 包裹着内边距和内容区域，可以设置：

```css
div {
    border-width: 3px;  /* 宽度 */
    border-style: solid;  /* 实线 solid、虚线 dashed、圆点 dotted */
    border-color: black;  /* 颜色 */

    /* 可简写为 */
    border: 3px solid black;
}
```

还可以单独设置每个边的宽度

![20250207164057](https://image.zuoright.com/20250207164057.png)

```html
<head>
    <style>
        .box {
            width: 200px;  /* 设置元素的宽度 */
            height: 100px; /* 设置元素的高度 */
            border: 5px solid black; /* 设置边框宽度 */
            margin-bottom: 20px;

            border-top-width: 10px; /* 设置顶部边框宽度 */
            border-right-width: 15px; /* 设置右侧边框宽度 */
            border-bottom-width: 20px; /* 设置底部边框宽度 */
            border-left-width: 25px; /* 设置左侧边框宽度 */
        }
    </style>
</head>
<body>
    <h1>Border Width Example</h1>
    <div class="box">上、右、下、左的边框宽度分别为 10px、15px、20px、25px</div>
</body>
```

设置圆角

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

    /* 上下 左右 */
    margin: 5% auto;

    /* 上 左右 下 */
    margin: 1em auto 2em;

    /* 顺时针：上 右 下 左 */
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

    /* 上下 左右 */
    padding: 5% 10%;

    /* 上 左右 下 */
    padding: 1em 2em 2em;

    /* 顺时针：上 右 下 左 */
    padding: 5px 1em 0 2em;
}
```

### 内容溢出

内容溢出虽然不会影响到内外边距本身的大小或位置，但溢出的内容可以覆盖相邻元素的内容或外边距区域，使用 `overflow` 属性可以控制元素溢出的方式，其只工作于指定高度的块元素上

```text
overflow: visible;  默认值，内容溢出会显示在元素框之外
overflow: hidden;  内容溢出部分不可见
overflow: scroll;  内容溢出显示滚动条，不溢出也显示
overflow: auto;  内容溢出显示滚动条，不溢出不显示
```

## Position

可以控制元素在页面上的精确位置

```text
position: static  默认，不定位

position: absolute  绝对定位，相对于最近的具有定位属性的（非默认的 static 定位）祖先元素（不一定是父元素）进行定位。如果没有这样的元素，则相对于初始包含块（通常是 <html> 元素）进行定位。绝对定位的元素脱离了正常的文档流，不会占据空间，其他元素会忽略它的存在。常用于精确定位元素，比如弹出菜单、工具提示等浮动的用户界面组件。

position: relative  相对定位，相对元素原本的位置进行移动，元素仍然保留在正常的文档流中，不会影响其他元素的布局

position: fixed  根据浏览器窗口进行定位，即使页面滚动，它也会停留在相同的位置。比如页面的 <footer> 部分通常就是固定在底部的。

position: sticky  根据用户的滚动位置进行定位。比如侧边栏或者返回顶部按钮等组件通常会用到。
```

![20250207102852](https://image.zuoright.com/20250207102852.png)

```css
div {
    position: relative;
    left: 100px;  /* 左边多出 100px，即向右移动 */
    top: 80px;  /* 上边多出 80px，即向下移动 */
}

div {
    position: sticky;
    top: 0;
}
```

## Float

可以让元素浮动到左侧或右侧，并且从正常流中移除

```text
flaot: none  默认，不浮动
flaot: left  浮动到左侧
flaot: right  浮动到右侧
flaot: inherit  继承父元素的浮动属性
```

通常用于实现文字环绕的效果

```html
<head>
    <style type="text/css">
        .box {
            float: left;
            width: 150px;
            height: 150px;
            margin-right: 30px;
        }
    </style>
</head>
<body>
    <h1>Simple float example</h1>
    <div class="box">Float</div>
    <p>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla luctus aliquam dolor, eu lacinia lorem placerat vulputate. Duis felis orci, pulvinar id metus ut, rutrum luctus orci. Cras porttitor imperdiet nunc, at ultricies tellus laoreet sit amet. Sed auctor cursus massa at porta. Integer ligula ipsum, tristique sit amet orci vel, viverra egestas ligula. Curabitur vehicula tellus neque, ac ornare ex malesuada et. In vitae convallis lacus. Aliquam erat volutpat. Suspendisse ac imperdiet turpis. Aenean finibus sollicitudin eros pharetra congue. Duis ornare egestas augue ut luctus. Proin blandit quam nec lacus varius commodo et a urna. Ut id ornare felis, eget fermentum sapien.
    </p>
</body>
```

![20240314204715](https://image.zuoright.com/20240314204715.png)

不过有时浮动会导致布局有问题，比如，一个父容器里有两个块级子元素，正常排列如图1，如果将红色块浮动到左边，则会与蓝色块重叠显示成图2的样式，则此时需要清除蓝色块的浮动，变为图3的样式。

![20240314223701](https://image.zuoright.com/20240314223701.png)

```html
<head>
    <style>
    .container {
        border: 2px solid #000;
    }
    
    .float-left {
        float: left;
        width: 50px;
        height: 50px;
        background-color: red;
    }

    .normal-block {
        clear: left;  /* 清除浮动 */
        width: 100px;
        height: 100px;
        background-color: blue;
    }
    </style>
</head>
<body>
    <div class="container">
    <div class="float-left"></div>
    <div class="normal-block"></div>
    </div>
</body>
```

## Flexbox

弹性盒子，是一种按行（row）或列（column）布局元素的一维布局方法，默认会根据浏览器的语言决定主轴是横轴还是纵轴（英文和中文都是从左到右）

![20240314230641](https://image.zuoright.com/20240314230641.png)

- 设定主轴方向

```css
.wrapper {
    display: flex;
    flex-direction: row;  /* 默认横轴 */
}
```

![20240314231805](https://image.zuoright.com/20240314231805.png)

```css
.wrapper {
    display: flex;
    flex-direction: column;
}
```

![20240314231758](https://image.zuoright.com/20240314231758.png)

- 常用属性

```text
justify-content: 控制 item 在主轴上的对齐方式
    flex-start 默认，左对齐
    flex-end 右对齐
    center 水平居中
    space-between 均分到父元素的两侧
    space-evenly 均分到子元素以外的空隙
    
align-items: 控制 item 在交叉轴上的对齐方式
    stretch 默认，如果项目未设置高度，或者设置为 auto，将占满整个容器的高度
    flex-start 上对齐
    flex-end 下对齐
    center 居中对齐
    baseline item 第一行基线（下边）对齐

flex-wrap: 溢出时如何处理
    nowrap 不换行
    wrap 向下换行
    wrap-reverse 向上换行
```

- 简写

```css
/* flex-direction + flex-wrap */
flex-flow: row nowrap;

/* justify-content + align-items */
align-content;
```

## Grid

网格，是由一系列水平及垂直的线构成的二维布局模式，行（row）与列（column）之间被称作沟槽（gutter）

![20240314232225](https://image.zuoright.com/20240314232225.png)

```css
.wrapper {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    gap: 20px;
}
```

![20240314232302](https://image.zuoright.com/20240314232302.png)

## 响应式设计

```html
<button class="menu">&#9776</button>
```

```css
.menu {
    display: none;  /* 隐藏菜单 */
}

/* 屏幕小于 768px 时的样式 */
@media screen and (max-width: 768px) {
    .menu {
        display: block;  /* 显示菜单 */
        background-color: transparent;  /* 透明背景 */
        color: white;  /* 设置文字颜色 */
        font-size: 35px;  /* 设置字体大小 */
        position: absolute;  /* 绝对定位 */
        top: 15px;  /* 设置上边距 */
        left: 20px;  /* 设置左边距 */
        border: none;  /* 去掉边框 */
        cursor: pointer;  /* 设置鼠标样式，hover 时显示手指 */
    }

    /* 导航 logo 居中 */
    header h1 {
        left: 50%;  /* 设置左边距，因为是从图片左上角定位的，所以使得 logo 偏右 */
        transform: translateX(-50%);
        /*
            transform 用于应用 2D 或 3D 转换效果，如旋转、缩放、平移或倾斜元素
            translateX() 函数用于在水平方向上平移元素，参数值可以是长度（如 px, em）或百分比，-50% 表示在 X 轴向左移动自身宽度的一半，使得图片整体水平居中
        */
    }
}
```
