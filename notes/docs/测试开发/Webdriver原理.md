# Webdriver原理

JSON Wire Protocol：https://zhuanlan.zhihu.com/p/24598811

mjwp：https://www.cnblogs.com/nbkhic/p/3752941.html

客户端即编写的脚本，发送请求到服务端，然后服务端会通过浏览器驱动（比如chromedriver，浏览器提供的一些api接口）与浏览器进行交互。

客户端程序库以各自的方式发起与服务器的会话：请求中包含一个被称作「预期能力（Desired Capabilities）」的 JSON 对象，这时服务器就会开启这个自动化会话，并返回一个用于发送后续命令的会话 ID。

C/S架构的好处就是server端可以不在本地，缺点是速度慢，cypress是？
