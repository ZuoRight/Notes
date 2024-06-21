# hardhat-config

## ABI

`npm install --save-dev hardhat-abi-exporter`

```js
// 用require引入插件:
require('hardhat-abi-exporter');
...

module.exports = {
    ...
    // 使用ABI Exporter插件:
    abiExporter: {
        // 输出到abi目录:
        path: "./abi",
        clear: false,
        flat: true,
        pretty: false,
        // 编译时输出:
        runOnCompile: true,
    }
};
```
