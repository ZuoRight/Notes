## 布局

## Normal

指不对页面进行任何布局控制时，浏览器默认的布局，各个元素完全按照源码出现的先后顺序显示。

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
<!DOCTYPE html>
<html>
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
</html>
```

![20240314204715](https://image.zuoright.com/20240314204715.png)

不过有时浮动会导致布局有问题，比如，一个父容器里有两个块级子元素，正常排列如图1，如果将红色块浮动到左边，则会与蓝色块重叠显示成图2的样式，则此时需要清除蓝色块的浮动，变为图3的样式。

![20240314223701](https://image.zuoright.com/20240314223701.png)

```html
<!DOCTYPE html>
<html>
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
</html>
```

## Position

可以控制元素在页面上的精确位置

```text
position: static  默认，不定位
position: relative  相对正常位置定位
position: absolute  相对于最近的非static定位的祖先元素（而不是它在HTML中的父元素）进行定位。如果没有这样的元素，那么它将相对于 <html> 元素进行定位。
position: fixed  相对于浏览器窗口进行定位，即使页面滚动，它也会停留在相同的位置。比如页面的 <footer> 部分通常就是固定在底部的。
position: sticky  根据用户的滚动位置进行定位。比如侧边栏或者返回顶部按钮等组件通常会用到。
```

```css
div {
    position: relative;
    top: 20px;  /* 向下移动20px */
    left: 20px;  /* 向右移动20px */
}

div {
    position: sticky;
    top: 0;
}
```

## Flex

弹性盒子，是一种按行（row）或列（column）布局元素的一维布局方法，默认会根据浏览器的语言决定主轴是横轴还是纵轴（英文和中文都是从左到右）

![20240314230641](https://image.zuoright.com/20240314230641.png)

```css
.wrapper {
    display: flex;
    flex-direction: row;  /* 设定主轴方向，默认横轴 */
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

常用属性

```text
flex-wrap 溢出时如何处理
    nowrap 不换行
    wrap向 下换行
    wrap-reverse 向上换行

justify-content 控制item在主轴上的对齐方式
    flex-start 默认，左对齐
    flex-end 右对齐
    center 水平居中
    
align-items 控制item在交叉轴上的对齐方式
    stretch 默认，如果项目未设置高度，或者设置为auto，将占满整个容器的高度
    flex-start 上对齐
    flex-end 下对齐
    center 居中对齐
    baseline item第一行基线（下边）对齐
```

简写

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
