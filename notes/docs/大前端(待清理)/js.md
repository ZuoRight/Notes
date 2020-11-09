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

JS

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
