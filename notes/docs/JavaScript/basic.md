# 值类型

## 两个特殊值

```js
// 未赋值，默认初始值为：undefined
let age;

// 无、空、值未知
let age = null;
```

## Number

JS 中的 Number 类型没有区分整数和浮点数，数字都被存储为双精度浮点数，所以存在精度限制：`-(2^53 - 1) ~ 2^53 - 1`

```js
let decLiteral = 6;
let floatNumber = 1.1;

// 科学表示法
let a = 1.024e3  //1024
let b = 1024e-3  //1.024

// 进制：0b, 0o, 0x
let binaryLiteral = 0b1010;  // 二进制数值
let hexLiteral = 0xf00d;  // 十六进制数值

let notANumber = 0/0;  // NaN 非数值 Not a Number
let infinityNumber = 1/0;  // Infinity 无穷大
let infinityNumber = -1/0;  // -Infinity 负无穷大
```

## BigInt

ES6 引入了 BigInt 可表示任意精度的整数

```js
// 在整数末尾加个n
const a = 9007199254740999n;

// 可以用BigInt()将其它类型转为BigInt，但被转换的数不能超过安全值
const b = BigInt(1000000000)  // 1000000000n
const b = BigInt("1000000000")  // 1000000000n
const b = BigInt("0x3b9aca00")  // 1000000000n
```

## String

```js
let str1 = 'Hi';  // 单引号
let str2 = "Hello";  // 双引号

console.log("hello %s", "7c")  // 格式化：%d 整数，%f 浮点数，%s 字符，%o 对象
console.log(`can embed another ${str}`);  // 反引号，也可叫做模版字符串，用于解析变量
```

- 转义符

```js
\n  // 换行
\r  // 回车
\t  // 制表符

// 版权符©
\251
\xA9
\u00A9
```

- 方法

```js
hello.length  // 5，获取长度
'hello'[1]  // e，索引
```

## Boolean

```js
let ageFieldChecked = false;
let nameFieldChecked = true;
```

以下值是 `false`

```js
undefined
null
0
NaN
""  // 包括 '' 和 ``
```

以下值是 `true`

```js
[]  // 空数组
{}  // 空对象
```

## Symbol

ES6 引入的基本类型，用于创建唯一标识符，不可变。主要用作对象属性的键，以避免属性名的冲突。

```js
// 创建 Symbol 时可以提供一个描述，描述是可选的，主要用于调试，没有其它作用
// 即使给两个 Symbol 相同的描述，它们也是不同的
let symbol1 = Symbol("mySymbol");
let symbol2 = Symbol("mySymbol");
console.log(symbol1 === symbol2); // 输出：false
```
