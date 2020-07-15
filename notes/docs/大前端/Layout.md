---
title: Layout
date: 2017-02-22 12:21:00
categories:
- 全栈，无栈
tags: CSS
top:
---
### W3C标准
由万维网联盟制定的一系列标准，倡导结构M、样式V、行为C分离    
包括：
* 结构化标准语言：HTML/XML
* 表现标准语言：CSS
* 行为标准语言：DOM、ECMAScript

### CSS定位机制：
* header
* mainbody：sidbar，content
* footer

<!-- more -->
***

### 标准文档流
> 从上到下，从左到右，由块级和行级元素组成(都属于盒子模型)

* 常见块级元素：div、ul、li、dl、dt、p
* 常见行级元素：span、strong、img、input

### 盒子模型
* 外边距（margin）: 四个参数：top、right、bottom、left
* 边框（border）：  三个参数：top、right/left、bottom
* 内边距（padding）:两个参数：top/bottom、right/left
* 内容（content）： 一个参数：top/right/bottom/left

### 盒子3D模型
* 第一层：border
* 第二层：content+padding
* 第三层：background-image
* 第四层：background-color
* 第五层：margin

***

### 常用      
```
*{margin:0; padding:0;};      

div class=wrap;    
```

### 浮动定位
**`float：left/right/none;`**          
> 会影响紧邻元素的位置，脱离标准文档流      
  元素会左/右移动，直至碰触到容器边缘为止        
  当设置了浮动而没设置宽度时，元素宽度会随内容的变化而变化       

**清除浮动：**    
* `clear:left/right/both;`  //当父包含快缩成一条时，此方法则无效     
* `width:100%;` + `overflow:hidden;`     


### 绝对定位    
> 可实现横向多列布局，和较复杂定位（带有遮罩层效果的提示框，固定层效果）     
  不会影响紧邻元素的位置，但会遮挡    

#### 静态定位static
* 默认状态

#### 相对定位relative   
* 当设置偏移量时，会相对自身初始位置偏移       
* 仍处于标准文档流中        
* 随即拥有偏移属性和z-index属性      

#### 绝对定位absolute     
* 当祖先元素没有设置相对定位，设置偏移量时，会以根节点（<html>）为参照建立以包含块为基准的定位         
* 当祖先元素设置了相对定位，设置偏移量时，会以最近设置了定位的节点为参照         
  完全脱离标准文档流       
  随机拥有偏移属性和z-index属性       
  不设置宽度时，会随内容大小变化         

#### 固定定位
fixed，也属于绝对定位         

***

### 自动居中一列布局      
> 使用到：标准文档流，块级元素，margin属性      
  `margin:0/Npx auto; width:Npx;`  //必须设置固定宽度       
  父级元素设置固定宽度（高度一般不设），子级元素设置100%        
  实线边框：`border:1px solid red;`         

### 横向两列布局       
> 使用到：float属性，使纵向排列的的块级元素变为横向排列         
  margin属性，设置两列之间的间距        

### 宽度自适应
> 父元素设置为相对定位       
  自适应元素设置为绝对定位         
  固定宽度列高度 > 自适应宽度的列      
