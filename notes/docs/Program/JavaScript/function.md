# 函数

```js
function sum(x=0, y=0) {
    return x + y;
}

sum(1, 2);  // 3
sum(undefined, 2)  // 2，x使用默认值
sum(1)  // 1，y使用默认值
```

- 解构式传参

```js
function sum({x=0, y=0}) {
    return x + y;
}

sum({x:1, y:2})  // 3
```

- 扩展运算符

```js
function addNumbers(x, y, z) {
  return x + y + z;
}

const numbers = [1, 2, 3];

// 使用扩展运算符将数组中的元素展开作为函数的参数
const result = addNumbers(...numbers);

console.log(result); // 输出: 6
```

## 匿名函数

- 函数表达式

```js
let sum = function(x, y) {
    return x + y;
};  // 注意末尾要带分号，表示赋值语句结束

data: function () { pass }
// 可简写为 data () { pass }
```

- 箭头函数

> ES6 新增

```js
let sum = (x, y) => {
    return x + y;
}

// 只有一个 return 语句时，可不写 return，并省略掉花括号
let y = x => x * x;
```

## 闭包

函数嵌套，内部函数作为外部函数的返回值

示例：统计 demo 函数被调用次数

- 非闭包实现

```js
let count = 0;  // 这里的count是全局变量，有可能会被其它函数修改

function demo() {
  count++;
  return count;
}

demo();  // 1
demo();  // 2
```

- 闭包实现，防止变量污染

```js
function demo() {
  let count = 0;  // 这里的count是局部变量，不会被其它函数修改

  return function () {
    count++;
    return count;
  };
}

let addCount = demo();

addCount(); // 1
addCount(); // 2
```
