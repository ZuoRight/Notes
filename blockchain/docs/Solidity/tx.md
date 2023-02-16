# Transaction

<https://solidity-by-example.org/sending-ether/>

send Ether

- `transfer` (2300 gas, throws error)
- `send` (2300 gas, returns bool)
- `call` (forward all gas or set gas, returns bool) <https://solidity-by-example.org/call/>

receive Ether

- `receive() external payable`
- `fallback() external payable`

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract SendEther {
    function sendViaTransfer(address payable _to) public payable {
        // This function is no longer recommended for sending Ether.
        _to.transfer(msg.value);
    }

    function sendViaSend(address payable _to) public payable {
        // Send returns a boolean value indicating success or failure.
        // This function is not recommended for sending Ether.
        bool sent = _to.send(msg.value);
        require(sent, "Failed to send Ether");
    }

    function sendViaCall(address payable _to) public payable {
        // Call returns a boolean value indicating success or failure.
        // call 是与其他合约交互的低级函数
        // 当您只是通过 fallback 函数发送 Ether 时，这是推荐使用的方法
        // 但这不是调用现有函数的推荐方法
        (bool sent, bytes memory data) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}

/*
Which function is called, fallback() or receive()?
        send Ether
            |
      msg.data is empty?
          /    \
        yes     no
        /         \
receive() exists?  fallback()
        /   \
    yes      no
    /          \
receive()   fallback()
*/
contract ReceiveEther {
    // Function to receive Ether. msg.data must be empty
    receive() external payable {}

    // Fallback function is called when msg.data is not empty
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```
