# 测试

> <https://hardhat.org/hardhat-runner/docs/guides/test-contracts>

- Hardhat-network-helpers
- Mocha
- Chai

```shell
npx hardhat test [test/my-tests.ts]
'
--grep "xxx" 指定测试块，类似it.only
--network localhost 指定网络
--parallel 并行测试
/

# 测试Gas消耗
REPORT_GAS=true npx hardhat test

# TS
yarn add @typechain/ethers-v5 @typechain/hardhat @types/chai @types/node @types/mocha ts-node typechain typescript
```

```ts
// 操纵账户
const [owner, otherAccount] = await ethers.getSigners();
```

使用 `Hardhat Network Helpers` 中的 `loadFixture` 替代 `beforeEach` 做一些代码重复的工作，类似 `Pytest` 的 `Fixture`

比如测试前部署合约，它只会部署一次，然后快照，每次测试完恢复状态，而不需要重新部署，以此节省时间

## mocha

<https://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html>

1. `describe()` 只是为了分组，可以嵌套到最深
2. `it()` 是一个测试用例
3. `before()`、`beforeEach()`、`after()`、`afterEach()` 是要运行的钩子

<https://mochajs.org/>

```shell
npm install --save-dev mocha
npm install --save-dev chai  # chai断言库

npm test [--grep "xxx"]  # 正则：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Regexp
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

```js
// 如果您需要通过从默认帐户以外的帐户（或 ethers.js 术语中的 Signer）发送交易来测试您的代码，您可以在 ethers.js 合约对象上使用 connect() 方法将其连接到不同的帐户
hardhatToken.connect(addr1).transfer()

describe("描述", 函数)


函数可以是匿名函数：function(){}
匿名函数可简写为箭头函数（又名“lambdas”）：() => {}

describe("描述", function () {
    beforeEach()
    it()
    // only，只测试这个  
    it.only("描述", () => {})
    // 嵌套
    describe("描述", () => {
        it("描述", () => {})
    })
})
```

## Hardhat Network Helpers

> <https://hardhat.org/hardhat-network-helpers/docs/overview>

### Time helpers

```js
import { time } from "@nomicfoundation/hardhat-network-helpers";

await time.latestBlock();  // 返回最新区块
await time.latest();  // 返回最新区块时间戳
await time.increase(amountInSeconds);  // 时间增加指定秒数
await time.increaseTo(timestamp)  // 时间增加到指定时间戳
await time.setNextBlockTimestamp(newTimestamp);  // 设置下一个区块的时间戳
```

## Hardhat Chai Matchers

> <https://hardhat.org/hardhat-chai-matchers/docs/overview>

`npm install --save-dev @nomicfoundation/hardhat-chai-matchers`

使用方式

```js
// hardhat.config.ts
import "@nomicfoundation/hardhat-chai-matchers";

// hardhat.config.js
require("@nomicfoundation/hardhat-chai-matchers")
```

- Events

```js
await expect().to.emit(contract, "Event");
await expect().to.emit().withArgs();

const { anyValue, anyUint } = require("@nomicfoundation/hardhat-chai-matchers/withArgs");
await expect().to.emit().withArgs(anyValue, xxx);
```

- Reverts

```js

await expect().to.be.reverted;
await expect().not.to.be.reverted;
await expect().to.be.revertedWith();

const { PANIC_CODES } = require("@nomicfoundation/hardhat-chai-matchers/panic");
await expect().to.be.revertedWithPanic(PANIC_CODES);

await expect().to.be.revertedWithCustomError(contract, "SomeCustomError");
```

- Equal

```js
await expect().to.equal(7)
```

- Balance Changes

```js
await expect(() => sender.sendTransaction({ to: someAddress, value: 200 })).to.changeEtherBalance(sender, "-200")

await expect(token.transfer(account, 1)).to.changeTokenBalance(token, account, 1)

await expect(token.transferFrom(sender, receiver, 1)).to.changeTokenBalances(token, [sender, receiver], [-1, 1]);
```

- Miscellaneous String Checks 字符串检查

```js
expect("0x1234").to.be.properHex(4);
expect("0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266").to.be.a.properAddress;
expect("0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80").to.be.a.properPrivateKey;
expect("0x00012AB").to.hexEqual("0x12ab");
```
