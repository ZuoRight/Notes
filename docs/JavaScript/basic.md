# 数据类型

## 特殊值 undefined 和 null

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

## Array

类似 Python 的 List，可以包含不同类型的元素

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

### `.map()`

`.map()` 方法在处理数据转换、创建新的数据结构以及保持代码的简洁性和可读性方面非常有用。

它接受一个回调函数和一个可选的 `this` 参数，返回一个新数组

回调函数有三个参数

```js
//  currentValue (必需) - 当前元素的值。
//  index (可选) - 当前元素的索引。
//  array (可选) - 调用 map 的数组。
function callback(currentValue, index, array) {
  // 你的逻辑
}
```

示例

```js
let numbers = [1, 4, 9, 16];

let roots = numbers.map(Math.sqrt);  // [1, 2, 3, 4]

// 使用箭头函数
const map1 = numbers.map(x => x * 2);
console.log(map1);  // [2, 8, 18, 32]

// 使用更复杂的回调函数
const map2 = numbers.map((num, index) => {
  return { index: index, value: num * 2 };
});
console.log(map2);
// [
//   { index: 0, value: 2 },
//   { index: 1, value: 8 },
//   { index: 2, value: 18 },
//   { index: 3, value: 32 }
// ]
```

值得注意的是 `map()` 方法是同步执行的，这意味着它会阻塞主线程直到所有元素都被处理完毕。

## Object

类似 Python 的 Dict，`{key: value}`，键是字符串，值可以是任意其它类型（包括函数）

```js
let obj = {
  name: '7c',
  age: 25,
  hello: function() {
    console.log('Hello');
  }
};

// 调用
obj["name"];
obj.age;
obj.hello();

// 存在则修改，不存在则新增
obj["age"] = 30
obj.gender = "male"

// 删除
delete obj.gender;  // 不存在或成功后返回 true
```

```js
// 获取所有key
Object.keys(obj);  // ['name', 'age', 'hello']

// 判断是否拥有某个key
obj.hasOwnProperty(x)

// 遍历
for (let key in obj) {
    console.log(key + ":" + obj[key]);  // 这里如果用obj.key会找不到
}
```

## Map

与 Obejct 相似，但 key 可以是任意类型，另外性能更优。

```js
let map = new Map();

map.set('key1', 'value1');
map.set('key2', 'value2');

console.log(map.size); // 输出: 2
console.log(map.get('key1')); // 输出: 'value1'
console.log(map.has('key2')); // 输出: true

map.delete('key1');
console.log(map.has('key1')); // 输出: false

map.clear();
console.log(map.size); // 输出: 0
```

## Set

值的集合，与 Python 的 Set 类似，不允许存储重复元素

```js
let set = new Set();

set.add('value1');
set.add('value2');
set.add('value1'); // 重复添加，不会有任何效果

console.log(set.size); // 输出: 2
console.log(set.has('value1')); // 输出: true

set.delete('value2');
console.log(set.has('value2')); // 输出: false

set.clear();
console.log(set.size); // 输出: 0
```

## Date

## Math
