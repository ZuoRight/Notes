# JS

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

## assert

Node.js 内置的断言模块

```js
import assert from 'assert';

assert(0 === false);
```

## Chai

一个流行的断言库，通常搭配测试框架使用，比如：Mocha + Chai

`npm install chai`

三种断言风格

### Assert

是对 Node.js 内置断言模块 assert 的扩展

```js
const chai = require('chai');
const assert = chai.assert;

assert.typeOf(5, 'number');
```

### Should

```js
const chai = require('chai');
const should = chai.should();

(5).should.be.a('number');
```

### Expect

允许更具描述性的断言，以及更深层次的比较，更强大

- `gte` greater than or equal to（大于或等于）
- `lte` less than or equal to（小于或等于）

```js
const chai = require('chai');
const expect = chai.expect;

const x = 7;

expect(x).to.be.a('number');
expect(x).to.equal("7")
expect(x).to.be.gte(5);  // 断言x是否大于等于5
```

## Mocha

```js
import assert from 'assert';

function sum(a, b) {
  return a + b;
}

// 通常使用 describe() 来分组，也可以自定义名字
describe('sum', () => {
    // it 用于定义具体的测试用例
    it('adds 1 + 2 to equal 3', () => {
      assert(sum(1, 2) === 3);
    });
  });
```

- 配置：`package.json`

```json
{
  "scripts": {
    "test": "mocha"
  }
}
```

- 执行：`npm test`
