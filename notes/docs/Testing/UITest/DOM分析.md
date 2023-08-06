# DOM分析

## 分析工具

### PC端

浏览器DevTools

### 移动原生应用

- Android ADT 自带的 uiautomatorviewer

需要基于jdk1.8版本

- Appium Desktop自带inspector

需要先配置Desired Capabilities

- [weditor](https://github.com/alibaba/web-editor)

阿里openatx团队开发的辅助工具

`pip install -U weditor`

命令行执行`weditor`，在自动打开的浏览器页面输入设备序列号，或ip:5555（需要先`adb tcpip 5555`开启无线连接端口）

### WebView页面

电脑连接手机或模拟器，浏览器访问：<chrome://inspect/#devices>，将会看到设备中打开的H5页面，点击inspect可以看到时时渲染的页面进行分析。

注意，如果想查看原生应用中嵌套的H5，需要应用打开WebView调试开关，模拟器中默认是打开的，但真机只能求助开发。

![20210704192754](http://image.zuoright.com/20210704192754.png)

## 定位方式

### Xpath

XML Path Language，适用于XML、HTML DOM、APP DOM等结构。

```palin
@属性

/div/p[1]  div子元素的第一个p元素

/div/p[last()]  div子元素的最后一个p元素

/div/p[position()<3]  div子元素的前两个p元素

/p[num>10]  选取值大于10的p元素

//* 选取所有元素
//*[@x="aaa"]  选取所有带x="aaa"属性的元素
//select[@x='a' and @y='b']  通过多个属性定位元素
```

```palin
//div[text()='谷歌']  找到文本是"谷歌"的div
//div[contains(text(), '谷歌')]")  找到文本含"谷歌"的div
//div[contains(@id,'in')]  找到所有id中包含有in的div
//div[starts-with(@id,'in')]  找到所有id以in开头的div
//div[starts-with(text()='百度')]  找到所有文本以"百度"开头的div
//div[starts-with(@id,'in') and contains(@id,'pu')]  找到所有id以in开头，并且id中包含pu的div
//div[starts-with(@id,'in') or contains(@id,'pu')]  找到所有id以in开头，或id中包含pu的div
//div[not(starts-with(@id,'in'))]  找到所有id不是以in开头的div

<!-- 轴：可定义相对于当前节点的节点集 -->
//a[text()='直播']/preceding-sibling::a[2]  先定位到文案为"直播"的a标签，然后找到它前面同级的所有a标签，选择其中的第2个
//a[text()='直播']/following-sibling::a[2]  同上，但是找后面的
```

### CSS Selector

参考：<https://www.runoob.com/cssref/css-selectors.html>

```css
*  所有元素
div
div,p  选择div和p元素

div p  选择div下各层级的所有p元素
div>p  选择div下第一级的所有p元素

div~p  选择div同级后面的所有p元素
div+p  选择div同级后面的第一个p元素

p:nth-of-type(2)  选择某元素下的第2个p元素
p:nth-last-of-type(2)  同上，倒数

p:nth-child(2)  选择元素的第2个子元素是p的所有p元素
p:nth-last-child(n) 同上，倒数

div:read-only  匹配设置了readonly属性的元素，注意要加read和only中间要加-

[attribute]
[attribute="aaa"]
[attribute~="aaa"]  attribute属性中包含"aaa"的元素
[attribute|="aaa"]  attribute属性中以"aaa"开头的元素
.class
#id
```

### JS

- Document

```javascript
ele = document.getElementById('id')
ele = document.querySelector('css')
ele = document.querySelectorAll('css')

ele.nodeType
ele.nodeName
ele.textContent
```

- JQuery

```javascript
ele = $('css selector')
ele = $x('xpath')

ele.text()  取值
ele.text('new content')  赋值

ele.find("xxx")  在结果中查找符合条件的
ele.filter("xxx")  在结果中过滤掉不符合条件的
```
