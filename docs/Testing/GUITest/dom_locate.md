# DOM 定位

## Xpath

XML Path Language，适用于 XML、HTML DOM、APP DOM 等结构。

```xml
// 表示XML选择器，在整个文档中搜索

//*  选取所有元素

@属性

//*[@x="aaa"]  选取所有带x="aaa"属性的元素
//select[@x='a' and @y='b']  通过多个属性定位元素

/div/p[1]  div子元素的第一个p元素

/div/p[last()]  div子元素的最后一个p元素

/div/p[position()<3]  div子元素的前两个p元素

/p[num>10]  选取值大于10的p元素

//div[text()='测试']  找到文本是"测试"的div

//li[contains(.,'测试')]  找到所有包含‘测试’的li元素
//div[contains(text(), '测试')]  找到文本含"测试"的div
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

## CSS Selector

参考：<https://www.runoob.com/cssref/css-selectors.html>

```css
*  所有元素
#id
.class

div
div:first-child input  第一个div元素下的input元素

div,p  div和p元素

div p  div下各层级的所有p元素
div>p  div下第一级的所有p元素

div~p  div同级后面的所有p元素
div+p  div同级后面的第一个p元素

p:nth-of-type(2)  某元素下的第2个p元素
p:nth-last-of-type(2)  同上，倒数

p:nth-child(2)  元素的第2个子元素是p的所有p元素
p:nth-last-child(n) 同上，倒数

div:read-only  匹配设置了readonly属性的元素，注意要加read和only中间要加-

[attribute]
[attribute="aaa"]
[attribute~="aaa"]  attribute属性中包含"aaa"的元素
[attribute|="aaa"]  attribute属性中以"aaa"开头的元素
```

### JS

- Document

```js
ele = document.getElementById('id')
ele = document.querySelector('css')
ele = document.querySelectorAll('css')

ele.nodeType
ele.nodeName
ele.textContent
```

- JQuery

```js
ele = $('css selector')
ele = $x('xpath')

ele.text()  取值
ele.text('new content')  赋值

ele.find("xxx")  在结果中查找符合条件的
ele.filter("xxx")  在结果中过滤掉不符合条件的
```
