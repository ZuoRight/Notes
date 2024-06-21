# Transaction

<https://solidity-by-example.org/sending-ether/>

## receive Ether

- `receive() external payable`
- `fallback() external payable`

不需要 function 关键字声明

```js
/**
Which function is called, fallback() or receive()?
        send Ether
            |
      msg.data is empty?
          /     \
        yes     no
        /         \
receive() exists?  fallback()
        /   \
    yes      no
    /          \
receive()   fallback()
*/
contract ReceiveEther {
    // 定义一个事件
    event Received(address Sender, uint Value);

    // Function to receive Ether. msg.data must be empty
    // 必须由 external 和 payable 修饰
    // 不能有任何参数，也不能返回任何值
    receive() external payable {
        // 接收ETH时触发Received事件
        emit Received(msg.sender, msg.value);
    }

    // Fallback function is called when msg.data is not empty
    // 在调用合约不存在的函数时被触发
    // 可用于接收ETH，也可以用于代理合约
    // 必须由external修饰，一般也会用payable修饰
    fallback() external payable {
        emit fallbackCalled(msg.sender, msg.value, msg.data);
    }

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

## send Ether

- `接收方地址.transfer(发送ETH数额)`

消耗上限为2300Gas，失败报错并回滚(revert)

```js
payable(msg.receiver).transfer(address(this).balance);

function transferETH(address payable _to, uint256 amount) external payable{
    _to.transfer(amount);
}
```

- `接收方地址.send(发送ETH数额)`

消耗上限为2300Gas，失败不会自动回滚，需要通过require语句判断返回bool值是否false

貌似很少人使用这个方法

```js
bool success = payable(msg.receiver).send(address(this).balance);

function sendETH(address payable _to, uint256 amount) external payable{
    bool success = _to.send(amount);
    // 处理返回值，如果失败，revert交易并发送error
    if(!success){
        revert SendFailed();
    }
}
```

- `接收方地址.call{value: 发送ETH数额}("")`

> call属于底层方法，可以调用几乎所有solidity函数，也可以用来转账

用call转账时没有Gas上限，失败返回false

call是官方推荐的通过触发fallback或receive函数发送ETH的方法

但是不推荐用call调用另一个合约，因为会转移权限

```js
// 两个返回值：(bool <调用是否成功true/false>, bytes <调用函数时接收函数的返回值>)
(bool success, ) = payable(msg.receiver).call{
        value: address(this).balance,
        gas: 5000
    }("");  // ("")里是被调用的函数，留空表示不调用任何函数，仅用来转账（所以才需要加{}）

function callETH(address payable _to, uint256 amount) external payable{
    (bool success,) = _to.call{value: amount}("");
    // 处理返回值，如果失败，revert交易并发送error
    if(!success){
        revert CallFailed();
    }
}
```
