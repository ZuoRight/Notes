# Mocha

Mocha + Chai

测试套件可以使用自定义名字，而不必须是 `describe()`

## Chai

- `gte` greater than or equal to（大于或等于）
- `lte` less than or equal to（小于或等于）

```js
// npm install --save-dev chai

assert.equal(A, B)
expect(A).to.equal(B)

const x = 8;
expect(x).to.be.gte(5);  // 断言x是否大于等于5
```
