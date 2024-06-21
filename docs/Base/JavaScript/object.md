# 对象类型

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

## Object

类似 Python 的 dict，`{key: value}`，键是字符串，值可以是任意其它类型（包括函数）

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

值的集合，与 Python的 Set 类似，不允许存储重复元素

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
