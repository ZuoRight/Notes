---
title: Appium
date: 2017-05-04 21:55:52
categories:
- 软件测试，的艺术
tags: 测试框架
---
# 介绍
1. Appium是一个移动端的自动化框架，可用于测试：
    * 原生应用：指用Android或iOS的用SDK编写的应用
    * 移动网页应用：指网页应用，类似于iOS中的Safari应用，或者Chrome应用或者类浏览器的应用
    * 混合型应用：指一种包裹Webview的应用，原生应用与网页内容交互性的应用
2. 跨平台的：可以用在osx，windows以及linux桌面系统上，针对不同的平台用一套api来编写测试用例


# 设计原则/特点
1. 采用底层驱动商提供的自动化框架
  * iOS：苹果的UIAutomation
  * Android 4.2+：谷歌的 UiAutomator
  * Android 2.3+：谷歌的Instrumentation（已被selendroid取代）
2. 采用底层驱动商提供的的统一API：Webdriver api
  * 也称为Selenium Webdriver，是一个C/S架构的协议，叫做JSON Wire Protocol
  * 通过这个协议，用任何语言写成的客户端都可以发送HTTP请求给服务器，这就意味着你可以自由选择你想要使用的测试框架和执行器，也可以将任何包含HTTP客户端的库文件加入到你的代码中
  * 在WebDriver的基础上，扩展了一些适合移动端自动化协议的API
3. 多语言：RobotFramework,Python,JS,JAVA,Ruby,PHP,C#
4. 开源
5. 无需重复造轮子，也就是说webdriver协议里的api已经够好了，拿来改进一下就可以了

<!-- more -->

# 安装Appium
  1. 直接官网[下载安装](http://pan.baidu.com/s/1o7PFvD4)
  2. 也可以安装node.js，用npm安装appium
  npm install -g appium

注：Appium是由.NET开发的，所以需要.NET framework相关组件，安装完后添加appium安装目录\node_modules\.bin到Path环境变量

# 创建自动化脚本

## 安装Appium Client
1. Appium Client是对webdriver原生api的一些扩展和封装，需要与原生webdriver配合使用，它可以帮助我们更容易的写出易懂的用例
2. 使用python开发并安装Appium-Python-Client
`pip install Appium-Python-Client`

## Android\tools中的一些工具
[参考](http://www.cnblogs.com/nbkhic/p/3806886.html)
1. uiautomatorviewer.bat
  主要用来查看控件的属性，比如resource id，class name等
  也可查看被测app的appPackage（Desired Capabilities中使用）
2. monitor.bat
  该工具可以帮我们找到android控件的content-description，为以后的find_element_by_accessibility_id 定位方法做参数使用

## 获取android app的Activity
三种方法：
1. 源码
2. 没有源码，可以[反编译工具](http://www.cnblogs.com/nbkhic/p/3806951.html)
3. 使用log查看大法

## 编写脚本
> [appium的基本概念](http://www.cnblogs.com/nbkhic/p/3803830.html)(乙醇)
> [Desired Capabilities详解](http://www.cnblogs.com/nbkhic/p/3805805.html)(乙醇)

## 运行
1. 启动安卓模拟器，或者连接上一个API Level17以上的android真机
2. 启动appium，或在命令行运行appium node
3. 运行脚本

# 控件定位
[基础](http://www.cnblogs.com/nbkhic/p/3807871.html)
[使用resource id定位](http://www.cnblogs.com/nbkhic/p/3813792.html)
