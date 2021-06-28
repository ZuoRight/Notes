# Selector

$('css')

$x('xpath')

## Xpath

@ 选取属性

```xpath
/div/p[1]  div子元素的第一个p元素

/div/p[last()]  div子元素的最后一个p元素

/div/p[position()<3]  div子元素的前两个p元素

/p[num>10]  选取值大于10的p元素

//* 选取所有元素
//*[@x="aaa"]  选取所有带x="aaa"属性的元素
//select[@x='a' and @y='b']  通过多个属性定位元素
```

`//span[text()="xxx"]`

<https://blog.csdn.net/wushuai150831/article/details/79047496>

## CSS

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
