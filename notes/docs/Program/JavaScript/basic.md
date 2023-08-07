# 基础

## 数据类型

可以用 `typeof x` 查看数据类型

> typeof 只是操作符，不过可以写成 `typeof(x)`，这里的 () 其实只是分组的作用，并不是函数

```js
// 共 8 种

typeof undefined // "undefined"
typeof null // "object" null其实是一个单独的类型，并不属于object，这其实是JS语言的一个错误

typeof 0 // "number"
typeof 10n // "bigint"
typeof "foo" // "string"
typeof true // "boolean"

typeof Math // "object" Math是内置的object
typeof [1, 2, 3]  // 'object' 数组也属于object类型
typeof alert // "function" 其实并没有函数类型，属于JS语言的错误，函数隶属于object类型，不过这个错误恰好方便一些情况的使用

typeof Symbol("id") // "symbol"
```

## 值类型

这些类型的数据被存储在栈内存中。当你把一个值类型的变量赋值给另一个变量时，新变量会获得原始变量的一个完整复制

### 两个特殊类型

```js
// 未赋值，默认初始值为：undefined
let age;

// 无、空、值未知
let age = null;
```

### Number

```js
let floatNumber = 1.1;

let decLiteral = 6;
let binaryLiteral = 0b1010;  // 二进制数值
let hexLiteral = 0xf00d;  // 十六进制数值

let infinityNumber = 1/0;  // 无穷大 Infinity
let infinityNumber = -1/0;  // 负无穷大 -Infinity
let notANumber = 0/0;  // 非数值 NaN
```

### BigInt

```js
// 超出安全整数范围（2^53 - 1 = 9007199254740991）会出现精度问题
console.log(9007199254740991 + 2); // 9007199254740992
// BigInt 类型，末尾加了个n
const bigInt = 1234567890123456789012345678901234567890n;
```

### String

```js
let str = "Hello";  // 双引号
let str2 = 'Single quotes are ok too';  // 单引号

console.log("hello %s", "7c")  // 格式化：%d 整数，%f 浮点数，%s 字符，%o 对象
console.log(`can embed another ${str}`);  // 反引号，也可叫做模版字符串，用于解析变量
```

### Boolean

```js
let nameFieldChecked = true;
let ageFieldChecked = false;

/*
以下值都是false
undefined
null
0
NaN
""
[]
*/
```

### Symbol

todo

## 引用类型

这些类型的数据被存储在堆内存中，变量实际上存储的是指向堆内存中该值的指针

如果想复制后的新对象与原对象互不影响，需要使用深拷贝

```js
let x = {
  name: "wtf",
  age: 18,
  arr: [],
  obj: {
    a: 1,
  },
};

let y = JSON.parse(JSON.stringify(x));  // 深拷贝

y.obj.a = 2;  // 不会影响 x.obj.a
```

### Array

与 Python 的 list 一样，可以包含不同类型的元素

```js
// 没有元素的空数组
const empty = [];
// 存储三个字符串的数组
const courses = ["Solidity", "Etherjs", "JavaScript"];
// 存储不同数据类型的数组
const mix = [1, 'WTF', true];
// 嵌套数组
const arr = [1, 2, 3, ["Solidity", true]]

arr.length;  // 数组长度，4
arr[0]  // 读取第1个元素，1
arr[3][0]  // 读取嵌套数组的第1个元素，"Solidity"
arr[1] = 2;  // 修改第2个元素

arr.push(7);  // 末尾添加
arr.pop();  // 末尾删除，返回删除的元素
```

- 遍历数组

```js
const numArr = [5, 8, 9, 11, 55];
let average = 0;

for (let i = 0; i < numArr.length; i++) {
  average += numArr[i] / numArr.length  // 相当于挨个计算每个元素的平均值，然后累加
}

console.log(`平均值为: ${average}`)  // 17.6
```

### Object

类似 Python 的 dict，`{key: value}`，值可以是任意其它类型，包括函数

```js
let obj = {
  name: '7c',
  age: 25,
  hello: function() {
    console.log('Hello');
  }
};

obj["name"];  // "7c"，读取方式1
obj.name;  // "7c"，读取方式2
obj.hello();  // 值为函数时的调用方式

obj.age = 30;  // 修改值
obj.gender = "male"  // 新增
delete obj.gender;  // 删除
```

- 遍历对象

```js
for (let key in obj) {
    console.log(key + ":" + obj[key]);  // 这里如果用obj.key会找不到
}

// 获取所有key
Object.keys(obj);  // ['name', 'age', 'hello']
```

## 运算符

- 算数运算符

```js
let num = 1 + 1;  // 2，加
let num = 2 - 1;  // 1，减
let num = 1 * 2;  // 2，乘
let num = 5 / 2;  // 2.5，除

let num = 2 ** 3;  // 8，求幂
let num = 5 % 2;  // 0.5，取余
let num = Math.floor(5 / 2);  // 2，取整

// 递增
let x = 1;
let y = x++; // 先赋值，后递增
let z = ++x; // 先递增，后赋值

// 递减
let x = 1;
let y = x--; // 先赋值，后递增
let z = --x; // 先递增，后赋值
```

- 比较运算符

JS是弱类型语言，不同类型比较时可能会存在隐式转换

推荐使用严格比较符（数据类型和值都相同）避免因此带来的隐患

```js
let bool3 = (2 > 1);  // true
let bool4 = (2 < 1);  // flase
let bool5 = (2 >= 1);  // true
let bool6 = (2 <= 1);  // flase

let bool1 = (5 == "5");  // true
let bool2 = (5 != "5");  // false
let bool7 = (5 === "5");  // flase 严格相等
let bool8 = (5 !== "5");  // true 严格不等
```

- 逻辑运算符

```js
true && false;  // 与
true || false;  // 或
!true;  // 非
```

## 语句

### 条件语句

- if...else...

```js
let x = 1;

if (x === 0) {
    console.log("x=0");
} else if (x === 1) {
    console.log("x=1");
} else {
    console.log("x=other");
}
```

- 三元运算符

```js
const r = x > y ? x : y;  // 如果x>y则r=x，否则r=y
```

### 循环语句

- for 循环

```js
for (循环变量初始化; 循环结束条件; 增量表达式) {
  执行语句
}

for (let i = 0; i < 3; i++) {
  console.log(`i 当前的值为：${i}`);
}

// i 当前的值为：0
// i 当前的值为：1
// i 当前的值为：2
```

- while

```js
let i = 0
while (i < 3) {
  console.log(`i 当前的值为：${i}`)
  i++
}

// 输出同上
```

- do...while

> 至少先执行一次语句，再做判断

```js
let i = 0
do {
  console.log(`i 当前的值为：${i}`);
  i++;
} while (i < 3)

// 输出同上
```

### 简单语句

- break
- continue

```js
if (i === 2) break;  // 终止整个循环
if (i === 2) continue;  // 中止本次循环
```
