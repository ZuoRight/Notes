# Ethers

<https://docs.ethers.org/v5/>

```shell
npm init
npm install --save ethers
```

## BigNumber

可以利用`ethers.BigNumber.from()`函数将string，number，BigNumber等类型转换为BigNumber

```js
const decimals = BigNumber.from("1000000000000000000");
const oneGwei = ethers.BigNumber.from("1000000000")

oneGwei.add(1).toString()  // 加
oneGwei.sub(1).toString()  // 减
oneGwei.mul(1).toString()  // 乘
oneGwei.div(1).toString()  // 除
oneGwei.mod(1).toString()  // 取模
oneGwei.pow(1).toString()  // 幂运算
oneGwei.abs(1).toString()  // 绝对值
oneGwei.eq(1000000000)  // 比较，是否相等，返回bool值
```

```ts
import { BigNumber } from "ethers";

const HOUR = BigNumber.from(60 * 60);  // 3600s
const DAY = HOUR.mul(24);  // 86400s
const WEEK = DAY.mul(7);
const MONTH = DAY.mul(30);
const YEAR = DAY.mul(365);
```

## 单位转换

```js
ethers.utils.formatUnits(变量, 单位);
```
