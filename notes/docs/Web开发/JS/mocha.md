# Mocha

<https://mochajs.org/>

```bash
npm install --save-dev mocha

npm test --grep "aa bb"
```

```js
var assert = require('assert');

// describe("测试组件名称", "回调函数") 是一种在 Mocha 中对我们的测试进行分组的方法。
// 目的是指明要测试的组件，嵌套的describe方法指明需要测试哪些方法
describe('Array', function() {
  describe('#indexOf()', function() {
    // it("解释测试应该做什么", "实际测试的回调函数") 用于单个测试用例
    it('should return -1 when the value is not present', function(){
      assert.equal(-1, [1,2,3].indexOf(4));
    });
  });
});
```
