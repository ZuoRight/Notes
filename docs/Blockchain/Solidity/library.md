# 库

库合约由 `library` 关键字声明

- 不能存在状态变量
- 不能够继承或被继承
- 不能接收以太币
- 不可以被销毁

一般我们不需要自己定义，使用用于定义一些标准库

## 常用库函数

- `SafeMath` 检查整数溢出
- `String`：将uint256转换为String
- `Address`：判断某个地址是否为合约地址
- `Create2`：更安全的使用Create2 EVM opcode
- `Arrays`：跟数组相关的库函数

### SafeMath

solidity 0.8 之前，safemath 是使用最多的的库，因为 int 和 uint 是运行在 unchecked 概念下的

uint 的最大数 255 再加 1 会变为 0，整数上溢，最小数 0 减 1 会变为 255，即整数下溢

可以使用 SafeMath 来进行溢出(overflow)检查

solidity 0.8 之后，默认会检查溢出，如果有必要可以使用：`unchecked{a = 255 + 1}` 变成跟旧版本一样，这样更节省 Gas

## 使用库

- 方式1：`using <库> for <某种数据类型>`

```solidity
import "./String.sol";

// 库函数会自动被添加为uint256型变量的成员
using Strings for uint256;
function getString1(uint256 _number) public pure returns(string memory){
    // 调用库函数的变量，会默认被当作第一个参数传入
    return _number.toHexString();
}
```

- 方式2：通过名称调用

```solidity
import "./String.sol";

// 直接通过库合约名调用
function getString2(uint256 _number) public pure returns(string memory){
    return Strings.toHexString(_number);
}
```
