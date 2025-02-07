# 尺寸及像素单位

像素（pixel）是显示的最小单元，一个像素只能表达一个色块

pixel 是一个合成词，`pi`cture + `x` +  `el`ement，即图像元素

参考：[An Overview of CSS Sizing Units](https://www.sitepoint.com/css-sizing-units/)

## 物理像素（Physical pixels）

也叫设备像素 (Device independent pixels)，是设备在出厂时固定好的，比如 iPhone6 的分辨率是 `1334px x 750px`，即手机竖屏时，长度是 1334 个物理像素，宽度是 750 个物理像素。

屏幕密度（Pixels per inch, PPI），意思是每英寸中有多少个物理像素。

![20240413164441](https://image.zuoright.com/20240413164441.png)

比如 iPhone6 为 326ppi

计算可知：$1inch/326ppi ≈ 0.003inch ≈ 0.0762mm$

即：$1in(英寸) ≈ 2.54cm(厘米) ≈ 6pc(派) ≈ 72pt(点) ≈ 96px(像素)$

## 逻辑像素（Logical pixels）

或称设备独立像素（Device Independent Pixels, DIPs），在 Web 开发中也叫「CSS 像素」

比如 iPhone6，通常使用 `667px x 375px` 的分辨率模拟在移动端上的展示，相对物理像素长宽各缩小了 2 倍，即面积缩小 4 倍，也就是说 1 个逻辑像素 = 4 个物理像素

随着移动设备的发展，屏幕尺寸不变的情况下，分辨率却越来越高，于是出现了一个设备像素密度比（Device Pixel Ratio, DPR）的概念，指一个逻辑像素对应多少物理像素

现代高分辨率屏幕（如 Retina 显示屏）这个比率通常是 2 或更高，意味着每个逻辑像素由 $2x2=4$ 个或更多的物理像素表示

可以简单的认为：$逻辑像素 ≈ 物理像素/DPR$

DPR 可通过编程接口来获取

```js
let dpr = window.devicePixelRatio;
let logicalHeight = screen.height;
let logicalWidth = screen.width;
```

## CSS 像素绝对单位

### `px`

```css
width: 200px;
height: 100px;
```

### 微信小程序像素单位 `rpx`

小程序中规定屏幕宽为 750rpx，根据不同的屏幕宽度 `rpx` 自动换算为不同大小的 `px`

比如 iPhone6 屏幕，物理像素宽度为 750，Web 开发中使用逻辑像素宽度为 375px，即 750 物理像素 = 375px = 750rpx，所以：1物理像素 = 1rpx = 0.5px

官方建议开发微信小程序时，设计师可以用 iPhone6 作为视觉稿的标准

![20240413171113](https://image.zuoright.com/20240413171113.png)

## CSS 像素相对单位

### 相对父元素的单位 `%`

```html
<div style="width: 600px; height: 400px;">
  <div style="width: 50%; height: 25%; padding: 5%; margin: 10%;">
    内容
  </div>
</div>

<!-- 在这段代码中，子元素的
宽度为：50% * 600px = 300px
高度为：25% * 400px = 100px
内边距为：5% * 600px = 30px
外边距为：10% * 600px = 60px -->
```

### 相对视窗的单位 `v~`

视窗（viewport）是用户在浏览器中可以看到的页面区域的大小，即网页可见区域的尺寸，不包括浏览器的工具栏、滚动条等。

视窗会随着设备的不同（比如手机、平板、桌面电脑）以及用户调整浏览器窗口大小的操作而改变。因此，使用相对视窗的单位可以创建响应式布局，根据不同设备的视窗大小自适应调整元素的尺寸。

```text
vw：1% 视窗宽度，即：1920px * 1% = 19.2px
vh：1% 视窗高度，即：1080px * 1% = 10.8px
vmin：min(vw, vh)
vmax：max(vw, vh)
```

100vw 或 100vh 则会覆盖整个窗口

### 相对字体的单位 `em`

```text
em：原本是一个打印术语，用于表示字母 M 的宽度，但在 CSS 中，它被用作一个相对于父元素字体大小的单位。
比如父元素 font-size 是 16px，那么 1em 等于 16px，意味着 em 单位可以根据父元素的字体大小动态缩放，非常适合实现响应式设计

rem：root em，即相对于根元素（在 HTML 文档中通常是 <html> 元素）的字体大小，非常适合于定义全局的样式基准

ex：基于给定字体中小写字母 x 的高度，不同字体和字体样式之间可能会有所不同，很少使用

ch：基于给定字体中数字 0 的平均宽度，在处理以字符为单位的宽度（比如输入框）时特别有用
```

## 元素尺寸

```css
/* 
    设置元素的宽度和高度，不继承，必须在每个元素上单独设置
    应用于所有块级元素，和某些行内块元素（如 <img>）
    默认值为 auto，元素的高度由其内容决定
*/
#a {
    width: 10px;
    height: 10px;
}

/* 设置最大最小宽度和高度，确保在不同屏幕和窗口尺寸下都能有良好的显示效果 */
#b {
    min-width: 20px;
    max-width: 100px;
    min-height: 10px;
    max-height: 50px;
}
```

## 文本尺寸

![20250207213527](https://image.zuoright.com/20250207213527.png)

基线是英文小写字母 x 的下沿

`line-height = font-size + 行距`，所以可通过控制行高的大小来控制行距

行距会被文字等分为上下两个半行距，当行高与容器高度相等时，单行文本则会居中

```css
{
  font-size: 20px;  /* 设置字体大小 */
}

/* 
    设置行高，会从父元素继承到子元素
    应用于块级元素（如 <div>、<p>），和行内元素（如 <span>、<a>）中的文本内容。
    默认值通常为 normal，其具体值取决于字体大小
*/
#c {
    line-height: 1.5em;  /* 设置行高为字体大小的 1.5 倍 */
}
```
