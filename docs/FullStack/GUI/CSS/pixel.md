# 像素

[An Overview of CSS Sizing Units](https://www.sitepoint.com/css-sizing-units/)

pixel 是一个合成词，`pi`cture + `x` +  `el`ement，即图像元素

像素是显示的最小单元，一个像素只能表达一个色块

![20240413164441](https://image.zuoright.com/20240413164441.png)

## 物理像素（Physical pixels）

也叫设备像素 (Device independent pixels)，是设备在出厂时固定好的，比如 iPhone6 的分辨率是 `1334px x 750px`，即手机竖屏时，长度是 1334 个物理像素，宽度是 750 个物理像素。

屏幕密度（Pixels per inch, PPI），意思是每英寸中有多少个物理像素。

比如 iPhone6 为 326ppi

计算可知：$1inch/326ppi ≈ 0.003inch ≈ 0.0762mm$

即：$1in(英寸) ≈ 2.54cm(厘米) ≈ 6pc(派) ≈ 72pt(点) ≈ 96px(像素)$

## 逻辑像素（Logical pixels）

或称设备独立像素（Device Independent Pixels, DIPs），在 Web 开发中也叫 CSS 像素

比如 iPhone6，通常使用 `667px x 375px` 的分辨率模拟在移动端上的展示，相对物理像素长宽各缩小了 2 倍，即面积缩小 4 倍，也就是说 1 个逻辑像素 = 4 个物理像素

随着移动设备的发展，屏幕尺寸不变的情况下，分辨率却越来越高，于是出现了一个设备像素密度比（Device Pixel Ratio, DPR）的概念，指一个逻辑像素对应多少物理像素

现代高分辨率屏幕（如 Retina 显示屏）这个比率通常是 2 或更高，意味着每个逻辑像素由 2x2=4 个或更多的物理像素表示

可以简单的认为：$逻辑像素 ≈ 物理像素/DPR$

DPR 可通过编程接口来获取

```js
let dpr = window.devicePixelRatio;
let logicalHeight = screen.height;
let logicalWidth = screen.width;
```

## CSS 尺寸单位

CSS 像素，即逻辑像素

### 绝对单位

最小、最大尺寸

```css
min-width: 20px;
max-width: 100px:
min-height: 10px;
max-height: 50px;
```

### 相对父节点尺寸的百分比 `%`

```html
<div style="width: 600px; height: 400px;">
  <div style="width: 50%; height: 25%; padding: 5%; margin: 10%;">
    内容
  </div>
</div>

在这段代码中，子元素的
宽度为：50% * 600px = 300px
高度为：25% * 400px = 100px
内边距为：5% * 600px = 30px
外边距为：10% * 600px = 60px
```

### 相对字体的单位

```text
em：原本是一个打印术语，用于表示字母 M 的宽度，但在 CSS 中，它被用作一个相对于父元素字体大小的单位。
比如父元素 font-size 是 16px，那么 1em 等于 16px，意味着 em 单位可以根据父元素的字体大小动态缩放，非常适合实现响应式设计

rem：root em，即相对于根元素（在 HTML 文档中通常是 <html> 元素）的字体大小，非常适合于定义全局的样式基准

ex：基于给定字体中小写字母 x 的高度，不同字体和字体样式之间可能会有所不同，很少使用

ch：基于给定字体中数字 0 的平均宽度，在处理以字符为单位的宽度（比如输入框）时特别有用
```

### 相对视口的单位

视口（viewport）是用户在浏览器中可以看到的页面区域的大小，即网页可见区域的尺寸，不包括浏览器的工具栏、滚动条等。

视口会随着设备的不同（比如手机、平板、桌面电脑）以及用户调整浏览器窗口大小的操作而改变。因此，使用相对视口的单位可以创建响应式布局，根据不同设备的视口大小自适应调整元素的尺寸。

```text
vw：1%视口宽度，即：1920px * 1% = 19.2px
vh：1%视口高度，即：1080px * 1% = 10.8px
vmin：min(vw, vh)
vmax：max(vw, vh)
```

## 微信小程序尺寸单位

小程序中规定屏幕宽为 750rpx，根据不同的屏幕宽度 `rpx` 自动换算为不同大小的 `px`

比如 iPhone6 屏幕，物理像素宽度为 750，Web 开发中使用逻辑像素宽度为 375px，即 750 物理像素 = 375px = 750rpx，所以：1物理像素 = 1rpx = 0.5px

官方建议开发微信小程序时，设计师可以用 iPhone6 作为视觉稿的标准

![20240413171113](https://image.zuoright.com/20240413171113.png)
