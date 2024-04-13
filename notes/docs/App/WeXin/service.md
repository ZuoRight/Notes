# 逻辑层 App Service

`getApp` 和 `getCurrentPages` 方法，分别用来获取 App 实例和当前页面栈。

```js
const appInstance = getApp()
console.log(appInstance.globalData)
```

## 注册小程序实例

```js
App({
  onLaunch (options) {
    // Do something initial when launch.
  },
  onShow (options) {
    // Do something when show.
  },
  onHide () {
    // Do something when hide.
  },
  onError (msg) {
    console.log(msg)
  },
  // 登录
  wx.login({
    success: res => {
      // 发送 res.code 到后台换取 openId, sessionKey, unionId
    }
  }),
  // 全局变量
  globalData: {
    userInfo: null
  }
})
```

## 页面构造器

### `Page({ })` 构造器，适合构造简单的页面

```js
const util = require('../../utils/util.js')

Page({
  data: {
    text: "This is page data."
  },
  onLoad: function(options) {
    // 页面创建时执行
  },
  onShow: function() {
    // 页面出现在前台时执行
  },
  onReady: function() {
    // 页面首次渲染完毕时执行
  },
  onHide: function() {
    // 页面从前台变为后台时执行
  },
  onUnload: function() {
    // 页面销毁时执行
  },
  onPullDownRefresh: function() {
    // 触发下拉刷新时执行
  },
  onReachBottom: function() {
    // 页面触底时执行
  },
  onShareAppMessage: function () {
    // 页面被用户分享时执行
  },
  onPageScroll: function() {
    // 页面滚动时执行
  },
  onResize: function() {
    // 页面尺寸变化时执行
  },
  onTabItemTap(item) {
    // tab 点击时执行
    console.log(item.index)
    console.log(item.pagePath)
    console.log(item.text)
  },
  // 事件响应函数
  viewTap: function() {
    this.setData({
      text: 'Set some data for updating view.'
    }, function() {
      // this is setData callback
    })
  },
  // 自由数据
  customData: {
    hi: 'MINA'
  }
})
```

### `Component({ })` 构造器，适合构造复杂的页面

```js
Component({
  data: {
    text: "This is page data."
  },
  // 方法需要放在 methods: { } 中
  methods: {
    onLoad: function(options) {
      // 页面创建时执行
    },
    onPullDownRefresh: function() {
      // 下拉刷新时执行
    },
    // 事件响应函数
    viewTap: function() {
      // ...
    }
  }
})
```

## 模块导入

- `common.js`

```js
function sayHello(name) {
  console.log(`Hello ${name} !`)
}
function sayGoodbye(name) {
  console.log(`Goodbye ${name} !`)
}

// 对外暴露接口
module.exports.sayHello = sayHello  // 方法1，推荐
exports.sayGoodbye = sayGoodbye  // 方法2，不推荐
```

- `xxx.js`

```js
// 引入模块
var common = require('common.js')

Page({
  helloMINA: function() {
    common.sayHello('MINA')
  },
  goodbyeMINA: function() {
    common.sayGoodbye('MINA')
  }
})
```

## API

### 事件监听 API

以 `on...` 开头，回调函数作为参数，在事件被触发时被调用

```js
wx.onCompassChange(function (res) {
  console.log(res.direction)
})
```

### 同步 API

以 `...Sync` 结尾，执行结果通过返回值直接获取

也有一些其他的同步 API 不以 `...Sync` 结尾，如 `wx.createWorker`，`wx.getBackgroundAudioManager` 等

```js
try {
  wx.setStorageSync('key', 'value')
} catch (e) {
  console.error(e)
}
```

### 异步 API

大多数 API 都是异步的

默认 promise 方式调用

```js
wx.chooseImage().then(res => console.log('res: ', res))
```

当接口参数 Object 对象中包含 `success/fail/complete` 时将按 callback 方式执行，无返回值。

```js
wx.chooseImage({
  success(res) {
    console.log('res:', res)
  }
})
```

### 云开发 API

```js
wx.cloud.callFunction({
  // 云函数名称
  name: 'cloudFunc',
  // 传给云函数的参数
  data: {
    a: 1,
    b: 2,
  },
  success: function(res) {
    console.log(res.result) // 示例
  },
  fail: console.error
})

// 此外，云函数同样支持promise形式调用
```
