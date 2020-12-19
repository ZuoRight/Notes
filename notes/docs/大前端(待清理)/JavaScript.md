# JavaScript

## Reference
* 《JavaScript+DOM编程艺术》（第2版）        
> _Jeremy Keith（吉姆·基斯）_              

* 《JavaScript高级程序设计》（第3版）        
> _Nicholas C. Zakas（尼古拉斯·泽卡斯）_        

* 《JavaScript语言精粹》（修订版）       
> _Douglas Crockford（道格拉斯·克劳克福德）_   

## DOM
> Document Object Model，可类比“世界对象模型”，在现实世界中，大家对很多事物都有一个共同的理解，属于一种API

### W3C定义
一个与系统平台和编程语言无关的接口，程序和脚本可以通过这个接口动态地对文档的内容、结构和样式进行访问和修改   
* D：文档，即网页文档   
* O：对象，DOM会在网页加载时根据网页文档创建一个文档对象      
* M：模型/Map，节点树  

### 工作模式
先加载文档的静态内容，再以动态方式对它们进行刷新，动态刷新不影响静态文档内容，即用户不需要刷新页面即可实现内容刷新     

***

### 节点Node     
> 元素节点：每个元素节点都是一个对象           
  文档节点      
  属性节点                

### 获取元素     
```document.getElementById(id)  //返回一个值           
document.getElementsByTagName(tag)  //返回一个数组
document.getElementsByClassName(class1 class2)  //返回一个数组       
```        
### 节点方法    
```getAttribute(attribute)  //检索属性值        
setAttribute(attribute,value)  //修改属性值    
```


## 变量·Variable
### 变量定义    
> var my_mood = "happy";         
  关键字 变量名 赋值 字面量 分号    
  **字面量（literal）**，可以在JS代码中直接写出来的数据内容，字面量除了它本身所给出的内容外无任何附加含义    
  **命名规则**：变量使用下划线分割单词`my_mood`，函数使用驼峰记号`myMood`          
  **作用域（scope）**：全局和局部                                                 

***

## 数据类型   
### 基本类型：
> 基本类型值，指简单的数据段,按值访问    
> **标量**：如果某个变量是标量的，它在任意时刻就只有一个值，字符串，数值和布尔值都是标量           

* Number:可用于表示所有数值，包括整数和浮点数  
* String
* Boolean  
* Undefined   
* Null  

### 引用类型:  
> 引用类型值，指那些可由多个值构成的对象，按引用访问，非离散
  引用类型的值是引用类型的一个实例，即对象（具有属性和方法）
  常被称为类，但不是类，因为它不具备传统语言所支持的类和接口等基本结构

***

## 语句·Statement
### 条件语句
`if(控制表达式1){...}else if(控制表达式2){...}else{...}`

`switch(任何数据类型的变量){case 1:...;break;case 2:...;break;case 3:...;break;default:...}`

***

## 函数Function
### 循环语句
#### 后测试语句
>代码至少被执行一次  

`do{...}while(控制表达式)`

#### 前测试语句
> 代码可能永不被执行，while循环做不到的for循环同样做不到，for循环只是把循环代码集中在了一个位置，较灵活

`while(控制表达式){...}`

`for(定义&初始化表达式;控制表达式;循环表达式){...}`

#### 迭代语句
> 可用来枚举对象的属性  

`for(xx in xx){...}`

### 跳出循环语句
#### break语句
退出循环  
#### continue语句
跳出本次循环，继续下次循环  
#### lable语句
常被引用于循环嵌套中的break或continue语句中
```自定义标签:for等循环语句等
break + lable` 退出指定循环（内部或外部循环）
continue + lable` 跳出内部循环，继续执行指定外部循环  
```

### 创建函数
> 因不存在函数签名的特性，所以没有重载，如果定义了两个同名函数，后定义的会覆盖先定义的           

#### 函数声明
> 函数声明的重要特征是函数声明提升，代码在之前会先读取函数声明，所以将函数放在其调用语句之后也正常运行    

```
faction 函数名(参数1,参数2,...){   
...;  函数体   
}
```

#### 函数表达式
> 此时的函数叫做匿名函数，也叫拉姆达函数
  与其他表达式一样，必须先赋值，而且结尾要加分号   

```
var faunctionName = faunction(arg0,arg1,arg2){   
...;  函数体   
};
```

### 函数的属性
```
.length  //参数个数           
.prototype
```

### 函数的方法
* .call()        
* .apply()       
* .bind()        

### 内部特殊对象   
#### arguments
```
arguments[0/1/2...]  //返回具体参数      
arguments.length  //返回参数个数      
arguments.callee  //指向正在执行的函数的指针      
```
#### this     
在网页全局作用域中，this对象引用的是window

### return语句
* 任何函数在任何时候都可以通过return语句返回值
* 函数在定义时不必指定是否返回值
* 函数会在执行完return语句后停止并立即跳出
* return语句可以不必带返回值，函数将在停止后返回undefined值

### 递归
即调用自身的函数，一个经典的递归阶乘函数：   
```
faunction factorial(num) {
  if (num <= 1 ){  
  return 1;    
  } else {    
  return num * arguments.calles(num-1);    
  } }
```    

### 闭包
* 在函数内部定义了其它函数，就创建了闭包     
* 闭包有权访问包含函数内部的所有变量        
* 通常函数的作用域及其所有变量都会被立即销毁            
* 但当函数返回了一个闭包时，这个函数的作用域将会一直在内存中保存到闭包不存在为止

***

## 对象
> 对象是自我包含的数据集合，包含在对象里的数据可以通过属性和方法访问               
  对象是由一些彼此相关的属性和方法集合在一起而构成的一个数据实体        
  **属性**：是隶属于某个特定对象的变量，`person.age`           
  **方法**：是只有某个特定对象才能调用的函数，`person.sleep`

> **实例**：          
  * 对象是统称（比如人），实例是个体，是对象的具体表现 （某某某）         
  * 创建新实例需要`new`关键字，当我们使用`new`去初始化一个数组时，其实就是在创建一个Array对象的新实例             

### 内置对象（native Object）
#### Array
> 数组是由名字相同的多个值构成的一个集合，即用一个变量存储一组值，集合中的每个值都是这个数组的元素（element）      
> **创建**    
  1. 构造函数（new可以省略）  
    * `var colors = new Array();`
    * `var colors = Array(3);`
    * `var colors = Array("red","blue","green");`
  2. 对象字面量  
    `var colors =["red","blue","green"];`

> **访问**  
  1. 数组长度  
    `colors.length`
  2. 索引  
    `colors[0/1/2]`

#### Date    
> **创建**
  1. 当前时间         
    `var now = new Date();`  
  2. 特定时间          
    `var date = new Date(Date.parse(February 7,2017))`  
    `var date = new Date(Date.UTC(2017,2,7,17,21))`  

#### RegExp
> **创建**
  1. 构造函数  
    `var expression = new RegExp{"正则表达式","标志（g/i/m）"};`  
  2. 对象字面量    
    `var expression = /正则表达式/标志（全局/不分大小写/多行）;`

#### Function   
> 函数实际上也是对象，每个函数都是Function类型的实例，而且与其他引用类型一样，也具有属性和方法

> **创建**    
    `var sum = Function(num1，num2)｛`    
    `return num1+num2;`     
    `};`  （末尾要有分号）         

***

### 单体内置对象
#### Global对象         
>    所有在全局作用域中定义的属性和函数，不属于任何其它对象的属性和方法，都是Global对象的属性和方法  
     window   

#### Math对象          
>    math.min()    
     math.max()  


### 宿主对象（host Object）    
> 预定义对象：预先定义好的、但不是JS本身的、而是由它的运行环境提供的对象     
  由web浏览器提供的预定义对象叫做宿主对象，例如`Form、Image、Element`等

> 最基础的是`window`对象
  window对应浏览器窗口本身，其属性和方法通常被称为BOM（浏览器对象模型），如window.open()，window.blur()          

> 但我们一般使用`document`对象来获得某给定网页上的任何一个元素信息，即浏览器内容，而不使用宿主对象       


### 用户定义对象（user-defined object）
#### Object
> **创建**  
  1. 构造函数    
      `var person = new Object();`  
      `person.name = "jc";`  
      `person.age = 25;`  
  2. 对象字面量  
      `var person = {`  
      `    name : "jc";`    
      `    age : 25`    
      `};`

> **访问**   
  1. 点表示法    
      `person.age`  
  2. 方括号语法      
      `person[first name]`  



# JS

## 基本语法

引入js时不必显要地把type类型指定为js，因为type默认就是js

```html
<script type="text/javascript"></script>
```

js语法与Java类似，语句结尾以分号结束，虽然不是必须，但浏览器执行js代码的引擎会自动给加上，所以为了不必要的问题，最好我们自己都给加上。

## 编程语言对比

js的this类似python的self
js/vue的$类似Python的__，表示内置

## 数据类型

JavaScript

```text
Number：不区分整数和浮点数
布尔值：true 和 false
null
undefined

字符串："string"
数组（类似Python的列表）：[a, b, c]
对象Object（类似Python的字典，键值对）：{a:1, b:2, c:3}

> 全局对象：window
> 对象的键必须用是字符串类型，但实际上其他数据类型作为键也没有没什么不合理的，所以ES6引入了新的数据类型Map
> 对象的值可以是任意数据类型，比如函数，即对象的方法

Map（更加类似Python的字典，二维数组）
var x = new Map([['a', 1], ['b', 2], ['c', 3]]);

Set（类似Python的集合）：只存储key，没有value
var x = new Set(['a', 'b','c']);
```


## 控制语句

### 判断语句

Python

```text
if 表达式:
    pass
elif:
    pass
else:
    pass
```

JS

可以不加{}，但为了不必要的麻烦，建议永远都加上

```text
if (表达式) {
    pass
} else if {
    pass
} else {
    pass
}
```

### 循环语句

Python

```text
for 表达式:
    pass
```

JS

```text
for (初始条件，结束条件，递增条件) {
    pass
}

while (表达式) {
    pass
}

// 开始时至少执行一次
do {
    pass
} while (表达式)
```

### 判断包含不包含

JS

```text
for (var key in X) {
    pass
}

// ES6引入的，用来统一循环iterable类型（Array，Map，Set）
for (var key of X) {
    pass
}
//也可以用iterable内置的forEach方法
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});

```

## 变量

### 动态语言（变量类型不固定）

Python

```text
# 定义变/常量：不用关键字，常量建议大写
x = "hello world"

# 格式化：{}.format(x)
```

JavaScript

```text
// 定义变量：var，js早期设计失误，并未强求用var申明，没有被var申明默认为全局变量，会带来一定的麻烦，ESMA后期推出严格模式强制用var申明（js代码第一行加上 'use strict';）
var x = "hello world"

// 定义局部变量（ES6）：let
for (let i=0; i<100; i++) {
    sum += i;
}

// 定义常量（ES6）
const PI

// 格式化
${x}

// 解构赋值：同时给多个变量赋值
var [x, y, z] = ['hello', 'JavaScript', 'ES6'];
```

## 函数

### 普通函数

Python

```text
def foo(x, y):
    pass
    return xxx
```

JavaScript

```text
function foo(x, y) {
    pass;
    return {
        xxx
    };
}

函数作为对象的值，即叫做方法
在一个方法内部，this是一个特殊的变量，它始终指向当前对象，可以通过this.x拿到对象的属性

一个只在函数内部起作用的关键字：arguments，通常用来判断函数传入的个数：arguments.length
```

### 匿名函数

JavaScript

```text
// 匿名函数（注意末尾要带分号，表示赋值语句结束）
var foo = function (x) {
    pass;
    return {
        xxx
    };
};

data: function () { pass } 可简写为 data () { pass }

// 箭头函数
x => {
    pass;
    return;
}
```

### 输出函数

Python

```text
print("hello world")
```

JS

```text
alert('Hello World');

// 控制台输出log
console.log("hello world")
```

## 面向对象

### Python：类和实例

```text
class Student():
    pass
```

### JS：没有类的概念

#### 方式1：基于构造函数（constructor）和原型链（prototype）

构造函数就相当于类，就是对象的模板，它就是个普通函数，但有自己的特征和用法，首字母一般大写。构造函数体内部使用了this关键字，代表了所要生成的对象/实例，建议启用严格模式，使函数内部的this不能指向全局对象。

```text
var Vehicle = function () {
    'use strict';
    this.price = 1000;
};
```

实例化时要用new命令，即执行构造函数，返回一个实例对象。

```text
var v = new Vehicle();  // 也可以不带括号，但建议带上
v.price // 1000
```

#### 方式2：Object.create()

没有构造函数时，还可以把现有的对象作为模板生成新的对象。

可以用obj.__proto__的方式将一个对象的原型指向任何对象，但一般不这样做，而是通过创建一个函数，然后使用Object.create()方法指定原型

```text
// 原型对象
var Student = {
    pass
};

// 创建一个创建原型的对象
function createStudent(name) {
    var s1 = Object.create(Student);  // 基于Student原型创建一个新对象
    s.name = name;  // 初始化新对象
    return s;
}

//实例化
var xiaoming = createStudent('小明');
```

#### 方式3：ES6 中新增了用class定义类

```text
class 类名 [extends 继承类] {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

## 提交表单



## JS

### 方式1

```text
<form id="test">
    <input type="text" name="test">
    <button type="button" onclick="doSubmitForm()">Submit</button>
</form>

<script>
function doSubmitForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input
    form.submit();  // js提交form，但通常不建议这样
    return true;  // 而是返回true，用form自己的onsubmit方法提交
}
</script>
```

### 方式2：AJAX

Web的运作原理：一次HTTP请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用JavaScript发送这个新请求，接收到数据后，再用JavaScript更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

AJAX全称：Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求，在现代浏览器上写AJAX主要依靠XMLHttpRequest对象。

### 方式3：Promise

Promise有各种开源实现，比如axios

## JQuery

jQuery的别名：$

## 错误处理

JS

```text
try ... catch ... finally
```

## Web 框架

Python

- Flask
- Djngo

Node.js

- Express/Koa

## 模块 Module

Python

```python
import
from X import x
```

Ruby：require
CSS：@import

JS（ES6+）

JS本身是没有模块的概念的，ES6 引入，ES6 的模块不是对象，而是通过export命令显式指定输出的代码，再通过import命令输入。（一个模块就是一个独立的文件，该文件内部的所有变量，外部无法获取，如果希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。）

ES6是编译时加载，即静态加载（运行时加载：动态加载），使得静态分析成为可能（可以进一步拓宽JS的语法，比如引入宏和类型检验）

正常输出：必须指定和输出一致的名字

```javascript
export function foo() {
    pass
};

import {foo} from '模块文件的位置';
```

默认输出：一个模块只能有一个默认输出，此时导入可以指定任意名字，且可以不加大括号

```javascript
export default function foo() {
    pass
}

import foo from '模块文件的位置';
```
