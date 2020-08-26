---
title: jQuery
date: 2017-02-18 13:52:05
categories:
- 全栈，无栈
tags: jQuery
---
## Reference
* 《锋利的jQuery》（第二版）       
> _单东林，张晓菲，魏然_    


## 选择器·Selector
### 定义DOM对象        
```
var  variable = DOM对象;            
var  variable = jQuery对象[0];       
var  variable = jQuery对象.get(0);            
```
### 定义jQuery对象          
```
var $variable = jQuery对象;           
var $variable = $(DOM对象);            
```
### 移交控制权        
```
jQuery.noConflict();  //Conflict(冲突)       
```
<!-- more -->
***
### jQuery选择器        
 特性：隐式迭代  
  $('')与$("")等价     

#### 检查某个元素是否存在
不能使用
```
if ($('#id')) {do something}  //因为jQuery获取的是对象
```
而应使用
```
if ($('#id').length > 0) {do something}  //应根据获取到的对象长度判断
```

#### 基本选择器      
```
$('#id')  //选择id               
$('.class')  //选择class              
$('element')  //选择元素     
$('a,b,c')  //选择多项          
$('*')  //选择所有   
```
#### 层次选择器      
```
$('a aa')  //选择a所【包含】内的【所有】aa            
$('a>aa')  //选择a所【包含】内的【子代】aa    
······························         
$('a+b')`   
$('a').next('b')  //选择a【之后】的与其【同辈】且【紧邻】b      
······························   
$('a~b')`   
$('a').nextAll('b')  //选择a【之后】的与其【同辈】的【所有】b          
······························      
$('a').siblings('b')  //选择a【前后】的与其【同辈】的【所有】b，siblings(兄弟姐妹)   
```
#### 过滤选择器
> 与css伪类选择器一样，以冒号:开头    

##### 基本过滤器                       
```
$('a:first')  //选择所有a中的【第一个】a
$('a:last')  //选择所有a中的【最后一个】a                         
$('a:not(b)') //选择所有a中的【不包含b】的a  
······························                 
$('a:even')  //选择所有a中的【索引为偶数】的a       
$('a:odd')  //选择所有a中的【索引为奇数】的a             
$('a:eq(1)')  //选择所有a中的【索引等于1】的a             
$('a:gt(1)')  //选择所有a中的【索引大于1】的a                 
$('a:lt')  //选择所有a中的【索引小于1】的a     
······························                       
$(':header')  //选择所有等级的【标题】               
$('a:animated')  //选择所有正在执行【动画】的a             
$(':focus')  //选择当前获取【焦点】的          
```
##### 内容过滤器      
```
$('a:has(b)')  //选择含有b的a          
$('a:contains('b')')  //选择含有文本为"b"的a         
$('a:parent')  //选择含有子元素/文本的a          
$('a:empty')  //选择不包含子元素/文本的空元素a  
```
##### 可见性过滤器       
> 包括：type="hidden" 、 style="display:none" 、 style="visibility:hidden"

```
$(':hidden')  //选取所有不可见元素   
$('a:hidden')  //只选取不可见的a                   
······························                   
$(':visible')  //选取所有可见元素  
$('a:visible')  //只选取可见的所有a元素   

.is(":visible")  //判断元素是否可见
```
##### 子元素过滤器    
```
$('a b:nth-child(n/2n/3n+1/even/odd)')  //选取每个a中的第n个元素，从1算起    
$('a b:first-child')  //选取每个a中的第一个b元素      
$('a b:last-child')  //选取每个a中的最后一个b元素  
$('a b:only-child')  //选取每个a中的只有一个b的b元素   
```
##### 属性过滤器      
```
$('a[attribute]')  //单个属性         
$('a[b1][b2][b3]')  //复合属性     
······························              
$('a[attribute=value]')  //属性等于               
$('a[attribute!=value]')  //属性不等于        
······························     
$('a[attribute^=value]')  //选取以value开始的attribute的a                
$('a[attribute$=value]')  //选取以value结束的attribute的a                
$('a[attribute*=value]')  //选取含有value的attribute的a           
······························           
$('a[attribute|=value]')  //选取等于value或以value-为前缀的attribute的a          
$('a[attribute~=value]')  //选取带空格且空格后面是value的a（attribute="xx value"）               
```
##### 表单对象属性过滤器（下拉框/多选框）       
```
$('input:enabled')  //选取表单内所有可用元素          
$('input:disabled')  //选取表单内所有不可用元素     
$('selected:checked')  //选取表单内所有被选中的元素（单选框/复选框    
$('selected:selected')  //选取表单内所有被选中的元素（下拉列表）     
```
#### 表单选择器      
```
$(':input')  //选取所有input、textarea、select、button元素    
$(':text')  //选取单行文本    
$(':password')  //选取密码框       
$(':radio')  //选取单选框        
$(':checkbox')  //选取复选框        
$(':submit')  //选取提交按钮         
$(':image')  //选取图像按钮      
$(':reset')  //选取重置按钮  
$(':button')  //所有按钮      
$(':file')  //选取上传域           
```

## 节点·Node
### 操作节点
#### 查找节点
jQuery选择器

#### 创建新节点      
```
$('html')  //$('<p tittle="text">文本<p/>')
```
#### 插入新节点/移动旧节点   
```
$(A).append(B)    
$(B).appendTo(A)  //将B追加到A中之后：A...BB'.A'  

$(A).prepend(B)   
$(B).prependTo(A)  //将B追加到A中之前：A.BB'...A

$(A).after(B)`     
$(B).insertAfter(A)  //将B追加到A后：AA'.BB'   

$(A).before(B)   
$(B).insertBefore(A)  //将B追加到A前：BB'.AA'  
```
#### 删除节点     
```
$(A).remove(B)  //删除后元素可恢复，但其绑定的事件不能恢复，参数B可选   
$(A).detach(B)  //删除后元素可恢复，与其绑定的事件也能恢复，参数B可选

$(A).empty()  //可清除A元素中的内容`    
```
#### 复制节点     
```
$(this).clone()  //只复制元素，不复制事件，所以副本元素不具备原元素的复制等功能   
$(this).clone(true)  //元素与事件一同复制，副本元素与原元素有同样功能  
```
#### 替换节点   
```
$(A).replaceWith(B)  
$(B).replaceAll(A)  //用B替换A，A的事件将不复存在   
```
#### 包裹节点  
```
$(A).wrap(B)  //用（多对）B元素包含（多对）A元素：BAA'B'..BAA'B'..BAA'B'     
$(A).wrapAll(B)  //用（一对）B元素包含（多对）A元素：B.AA'AA'AA'.B'（中间的其他元素也会被包裹）
$(A).wrapInner(B)  //用B元素包含A元素的子内容：AB...B'A'      
```

***

## 属性·Property
### 操作属性
> jQuery中很多方法都是同一个函数既可实现获取（getter）又可设置（setter）                
  例如：.attr()，.html()，.text()，.height()，.width()，.val()，.css()     

```
.attr(A)  //获取节点属性      
.attr(A,a)  //设置单个节点属性A=a     
.attr(A:a,B:b,C:c)  //设置多个节点属性   
.removeAttr()  //删除属性    

.pop(A)  //获取匹配到的首个节点的属性  
.removePop()  //删除匹配到的首个节点的属性  
```
### 操作样式     
```
.attr()  //获取/设置样式同获取/设置属性一样   

.addClass()  //追加样式   
.removeClass()  //移除样式，不带参数则移除所有样式     
.removeClass(a)  //带一个则移除单个样式    
.removeClass(a b)  //空格分隔则移除多个样式   

.toggle(A,B)  //会在A与B间切换样式    
.toggleClass(A')  //会在A与A'间切换样式    
.hasClass(A')  //判断A是否有A'样式，如果有则返回true，若没有则返回false     
```
### 其他       
```
.html()  //获取/设置html代码（需传参），类似JS的innerHtml属性    
.text()  //获取/设置文本内容（需传参），类似JS的innerText属性    
.val()  //获取/设置元素的值（需传参），表单元素的默认值可表示为：.defaultValue    
```
### 遍历节点   
```
.children()  //获取匹配元素的所有子元素     

.next()  //获取匹配元素后面的同辈紧邻元素  
.nextAll()  //获取匹配元素后面的所有同辈元素   
.pre()  //获取匹配元素前面的同辈紧邻元素    
.preAll()  //获取匹配元素前面的所有同辈紧邻元素   
.siblings()  //获取匹配元素前和后的同辈紧邻元素    

.closest()  //获取最近的匹配元素，首先检查当前元素是否匹配，不匹配则逐级向上查找，若没有则返回空对象   
.parent()  //获取每个匹配元素的父元素     
.parents()  //获取每个匹配元素的父元素以及父元素的父元素等祖先元素      
```

### 设置css样式
```
.css(A)  //读取style对象属性      
.css(A,a)  //设置单个style对象属性A=a    
.css(A:a,B:b,C:c)  //设置多个style对象属性   
.css('opacity','0.5')  //设置透明度为半透明  

.height(100)  //获取/设置高度，默认单位为px，如需使用其他单位：.height(3em)
.width(100)  //获取/设置宽度，默认单位为px，如需使用其他单位：.width(3em)   

.offset()  //获取元素在当前视窗的相对偏移，返回对象包含两个属性       
.offset().top     
.offset().left     

.position()  //获取元素与最近的position属性为relative/absolute祖/父节点的相对偏移   
.position().top  
.position().left  

.scrollTop()        
.scrollLeft()  //获取元素的滚动条距离上/左的距离，传入参数可控制滚动条到指定位置     
```

***
## 事件·Event
### 加载事件
#### JS中加载事件   
```
window.onload()  //需要页面中所有元素都加载完才能被执行，一次只能用一个，多个同时使用后面的会覆盖前面的      
```
#### jQuery中加载事件：   
```
$(document).ready()  //只要DOM加载完即可执行，可多个同时使用，依次被执行    
$().ready()  //$()不带参数默认就是$(document)

$(function(){}) //最简洁的写法    

$(A).load()  //当A为window时等价于JS中的window.onload()方法     
```
### 绑定事件   
```
.bind（"event1 event2",[.data],fn）  //[.data]为可选参数，可用来添加事件命名空间等      
.event(fn(){})  //简写
```

#### event包括以下事件   
1. focus(聚焦),blur(失去聚焦),load,resize,scroll,unload,click,dblclick       
2. mousedown,mouseup,mousemove,mouseover,mouseout,mouseseebter,mouseleave       
3. change,select,submit,keydown,keypress,keyup,error    

```
.unbind([event],[fn])  //移除事件，不带参数则移除所有事件   
.one(event,fn)  //绑定一次，执行一次后自动删除  
```
### 合成事件    
```
.hover(fn(enter){},fn(leave){})  //模拟光标悬停/移出事件    

.toggle(fn1,fn2,···fnN)  //1.模拟鼠标连续点击事件 2.切换元素可见状态`    
```
#### 创建事件对象           
```
.event（fn(参数){}）  //即给函数一个参数即可    
```
#### 事件对象的属性    
```
参数.type()  //获取事件类型    
参数.target()  //获取触发事件的元素   
参数.stopPropagation();  //停止事件冒泡（从内到外触发事件，从外到内触发事件叫做事件捕获，但jQuery不支持）  
参数.preventDefault();  //阻止默认行为，若想同时阻止冒泡和默认行为，可以使用return false    
```
#### 模拟事件    
```
.trigger(type,[data])  //模拟用户点击鼠标等行为    
```
#### 内置动画   

```
.show()  //会改变内容的高度，宽度，不透明度
.hide([slow/normal/fast/xx毫秒])  //slow=600,normal=400,fast=200毫秒
```

```
.fadeIN()  //只改变不透明度，渐显
.fadeOut()  //渐隐
```
```
.slideUp()  //只改变高度，延伸   
.slideDown()  //缩短    
```

#### 自定义动画    
```
animate(params,speed,callback)
```    
> params：一个包含样式属性及值的映射{A:a,B:b}    
  speed：速度参数，可选    
  callback：在动画完成时要执行的函数    

```
.is(":animate")  //判断元素是否处在动画状态
```  

#### 交互动画  
`toggle`     
`slideToggle`      
`fadeTo`     
`fadeToggle`       

***

## 表单·FormTable
单行文本框   
多行文本框   

密码框   
隐藏域   

单选框   
复选框   
下拉框   

文件上传框   
