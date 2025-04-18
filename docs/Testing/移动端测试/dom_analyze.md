# 移动端 DOM 分析

移动端也有一些工具可以像浏览器的 DevTools 一样分析 DOM

## 原生应用

- Android ADT 自带的 uiautomatorviewer

需要基于jdk1.8版本

- Appium Desktop 自带的 inspector

需要先配置 Desired Capabilities

- [weditor](https://github.com/alibaba/web-editor)

阿里 openatx 团队开发的辅助工具

`pip install -U weditor`

命令行执行 `weditor`，在自动打开的浏览器页面输入设备序列号，或 ip:5555（需要先 `adb tcpip 5555` 开启无线连接端口）

## WebView 页面

WebView（网页视图）是一个在应用程序中显示网页内容的组件或控件，允许开发者在自己的应用中嵌入一个浏览器引擎，用于加载和显示网页内容

如果想查看原生应用中的 WebView 页面，需要应用打开 WebView 调试开关，模拟器中默认是打开的，但真机只能求助开发。

### Android

[参考文档](https://developer.chrome.com/docs/devtools/remote-debugging?hl=zh-cn)

电脑连接手机或模拟器，电脑端 Chrome 访问：`chrome://inspect/#devices`，将会看到移动端 Chrome 中打开的 H5 页面，点击 inspect 可以看到时时渲染的页面进行分析。

![20210704192754](https://image.zuoright.com/20210704192754.png)

### iOS

可以用 Safari 浏览器来调试 iOS 上 Chrome 或 Safari 中的 H5，以及应用中的 Webview 页面

1. 打开 iOS 中 Safari 或 Chrome 的网页检查器

![20230905133024](https://image.zuoright.com/20230905133024.png)

2. 打开 Mac 中 Safari 的开发

![20230905133101](https://image.zuoright.com/20230905133101.png)

3. 重启手机浏览器，打开要调试的页面，将 iOS 设备连接到 Mac，即可在开发中看到

![20230905133450](https://image.zuoright.com/20230905133450.png)
