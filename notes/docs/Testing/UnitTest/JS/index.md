# 引言

```js
let demoError = new Error("This is an error message");  // 创建一个Error对象

console.log(demoError.name);  // "Error"
console.log(demoError.message);  // "This is an error message"

throw demoError  // 手动抛出错误
```

## 错误处理

```js
try {
    // 可能会抛出错误的代码
    const a = 1
    a() // 这里会抛出一个 TypeError
} catch (error) {
    // 处理错误
    console.log(error.message) // 输出 'a is not a function'
} finally {
    // 无论是否抛出错误，这里都会被执行
    console.log('执行完毕')
}
```
