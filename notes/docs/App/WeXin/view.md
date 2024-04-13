# 视图层

- 组件(Component) 是视图的基本组成单元
- WXML(WeiXin Markup language) 结合基础组件、事件系统，可以构建出页面的结构
- WXSS(WeiXin Style Sheet) 用于描述页面的样式
- WXS(WeiXin Script) 结合 WXML 可以构建出页面的结构

## 组件

所有组件与属性都是小写，以连字符 `-` 连接

### 属性

所有组件都有以下属性

![20240413231222](https://image.zuoright.com/20240413231222.png)

## WXML

### 数据绑定

```xml
<!--wxml-->
<view> {{message}} </view>
```

```js
// page.js
Page({
  data: {
    message: 'Hello MINA!'
  }
})
```

也可以绑定到属性上

```XML
<!-- 单向绑定，this.data.value的值不会根据输入值改变 -->
<input value="{{value}}" />

<!-- 双向绑定，this.data.value的值会根据输入值改变，继而影响所有绑定了value的变量 -->
<input model:value="{{value}}" />
```

### 列表渲染

```xml
<!--wxml-->
<view wx:for="{{array}}"> {{item}} </view>
```

```js
// page.js
Page({
  data: {
    array: [1, 2, 3, 4, 5]
  }
})
```

### 条件渲染

```xml
<!--wxml-->
<view wx:if="{{view == 'WEBVIEW'}}"> WEBVIEW </view>
<view wx:elif="{{view == 'APP'}}"> APP </view>
<view wx:else="{{view == 'MINA'}}"> MINA </view>
```

```js
// page.js
Page({
  data: {
    view: 'MINA'
  }
})
```

### 模版

```xml
<!--wxml-->
<template name="staffName">
  <!-- 模板拥有自己的作用域，只能使用 data 传入的数据 -->
  <view>
    FirstName: {{firstName}}, LastName: {{lastName}}
  </view>
</template>

<!-- 扩展运算符 ... 可以将对象或数组进行解构赋值 -->
<template is="staffName" data="{{...staffA}}"></template>
<template is="staffName" data="{{...staffB}}"></template>
<template is="staffName" data="{{...staffC}}"></template>

<!-- block 标签不是组件，只是一个包装元素 -->
<block wx:for="{{[1, 2, 3, 4, 5]}}">
  <!-- is 属性可以使用 Mustache 语法动态渲染 -->
  <template is="{{item % 2 == 0 ? 'A' : 'B'}}"/>
</block>
```

```js
// page.js
Page({
  data: {
    staffA: {firstName: 'Hulk', lastName: 'Hu'},
    staffB: {firstName: 'Shang', lastName: 'You'},
    staffC: {firstName: 'Gideon', lastName: 'Lin'}
  }
})
```

## WXSS

### 内联样式

```xml
<!-- 静态样式 -->
<view class="normal_view" />

<!-- 动态样式 -->
<view style="color:{{color}};" />
```

## 外部样式

直接写在相应的 `<page>.wxss` 中即可

```css
/** app.wxss **/
/* 引入其它文件样式 */
@import "common.wxss";

.middle-p {
  padding: 15px;
}
```

```css
/** common.wxss **/
.small-p {
  padding: 5px;
}
```

### 选择器

```text
.intro  选择所有拥有 class="intro" 的组件
#id  选择拥有 id="firstname" 的组件
view  选择所有 view 组件
view, checkbox  选择所有文档的 view 组件和所有的 checkbox 组件
view::after  在 view 组件后边插入内容
view::before  在 view 组件前边插入内容
```

## WXS

```xml
<!--wxml-->
<wxs module="m1">
  var msg = "hello world";
  <!-- getMax 函数，接受一个数组，且返回数组中最大的元素的值 -->
  var getMax = function(array) {
    var max = undefined;
    for (var i = 0; i < array.length; ++i) {
      max = max === undefined ?
        array[i] :
        (max >= array[i] ? max : array[i]);
    }
    return max;
  }

  module.exports.message = msg;
  module.exports.getMax = getMax;
</wxs>

<!-- 输出 hello world -->
<view> {{m1.message}} </view>
<!-- 调用 wxs 里面的 getMax 函数，参数为 page.js 里面的 array -->
<view> {{m1.getMax(array)}} </view>
```

```js
// page.js
Page({
  data: {
    array: [1, 2, 3, 4, 5, 1, 2, 3, 4]
  }
})
```

也可以把 wxs 函数写在 `.wss` 文件中引入

```xml
<wxs module="m2" src="./test.wxs"></wxs>
<view id="tapTest" data-hi="Weixin" bindtap="{{m2.tapName}}"> Click me! </view>
```

```js
// text.wsx
// wxs 函数绑定事件，接受两个参数：event和ownerInstance
function tapName(event, ownerInstance) {
  console.log('tap Weixin', JSON.stringify(event))
}
module.exports = {
  tapName: tapName
}
```

## 事件

当一个组件上的事件被触发后，该事件会向父节点传递，则被称为冒泡事件，反之为非冒泡事件。

语法：`bind<eventName>` 或 `bind:<eventName>`

```xml
<!-- bindtap 也可以写成 bind:tap -->
<view bindtap="handleTap">
    Click here!
</view>

<!--
可以是一个数据绑定 
this.data.handlerName 必须是一个字符串，指定事件处理函数名；
如果它是个空字符串，则这个绑定会失效，可以利用这个特性来暂时禁用一些事件
-->
<view bindtap="{{ handlerName }}">
    Click here!
</view>
```

也可以用 `catch` 来绑定事件，但会阻止事件向上冒泡

```xml
<view id="outer" bindtap="handleTap1">
  outer view
  <!-- 点击 middle view 只会触发 handleTap2 -->
  <view id="middle" catchtap="handleTap2">
    middle view
    <!-- 点击 inner view 会先触发 handleTap3 然后触发 handleTap2 -->
    <view id="inner" bindtap="handleTap3">
      inner view
    </view>
  </view>
</view>
```

向上冒泡的过程中，用 `mut-bind` 绑定的事件之间会互斥，但不影响 `bind` 事件

```xml
<!-- 点击 inner view 先触发 handleTap3，然后跳过 handleTap2 继续向上冒泡触发 handleTap1 -->
<view id="outer" bindtap="handleTap1">
  outer view
  <view id="middle" mut-bind:tap="handleTap2">
    middle view
    <view id="inner" mut-bind:tap="handleTap3">
      inner view
    </view>
  </view>
</view>
```

触摸类事件支持使用 `capture-bind` 方法捕获事件，捕获阶段要早于冒泡阶段，且顺序与冒泡相反，是由外向内一层层捕获

同理，可使用 `capture-catch` 阻止向内继续捕获

```xml
<!-- 事件触发顺序为 handleTap1～4 -->
<view id="outer" bind:touchstart="handleTap4" capture-bind:touchstart="handleTap1">
  outer view
  <view id="inner" bind:touchstart="handleTap3" capture-bind:touchstart="handleTap2">
    inner view
  </view>
</view>
```
