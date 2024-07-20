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

电脑连接手机或模拟器，浏览器访问：<chrome://inspect/#devices>，将会看到设备中打开的H5页面，点击 inspect 可以看到时时渲染的页面进行分析。

注意，如果想查看原生应用中嵌套的H5，需要应用打开 WebView 调试开关，模拟器中默认是打开的，但真机只能求助开发。

![20210704192754](http://image.zuoright.com/20210704192754.png)
