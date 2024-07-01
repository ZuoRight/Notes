# 引言

端到端 E2E, End-to-End 测试，通过模拟真实用户场景和工作流程来验证整个应用程序是否按预期工作

不仅仅测试应用的单个功能或组件，而是测试整个应用的工作流程，这包括用户界面、网络通信、数据库操作、后端服务等。

- UFT(QTP)
- Selenium
- Appium
- Cypress: <https://www.cypress.io/>

## Webdriver

Selenium 和 Appium 等大部分框架都是基于 [W3C WebDriver](https://w3c.github.io/webdriver/) 

> [JSON Wire 协议](https://www.selenium.dev/documentation/legacy/json_wire_protocol/) 是 WebDriver 的前身是

WebDriver 采用 C/S 架构的通信模式，基于 HTTP 协议。测试脚本（客户端）通过发送命令到 WebDriver 服务器，WebDriver 服务器再将这些命令转换为对应的浏览器动作

每种主流浏览器都有相应的驱动程序（如ChromeDriver、GeckoDriver等），这些驱动程序实现了WebDriver协议，并能与各自的浏览器进行交互。这些驱动程序负责解释WebDriver命令，并将其转化为浏览器能理解的操作。

客户端即编写的脚本，发送请求到服务端，然后服务端会通过浏览器驱动（比如chromedriver，浏览器提供的一些api接口）与浏览器进行交互。

优点是 Server 端可以分布式部署，并支持多语言，缺点是速度较慢

## Cypress

Cypress 直接在浏览器中运行，消除了网络延迟和协议转换的开销

可以直接访问操作应用的 DOM 结构和 JavaScript 运行环境，能监听应用中的各种事件，并根据事件结果动态调整测试流程。

还能够控制应用的所有网络请求，拦截和修改网络请求和响应，方便测试中模拟各种网络场景。

允许开发者编写断言来验证应用的状态。这些断言直接在浏览器中评估，提供即时的反馈。

在测试执行过程中，Cypress 可以自动捕捉屏幕截图和视频记录。

## Puppeteer

Chrome 开发团队在 2017 年发布的一个 Node.js 包，用来模拟 Chrome 浏览器的运行

> 比较早的类似工具是 PhantomJS，基于 WebKit，已停止维护

Headless Chrome 是指在无界面的环境中通过命令行或脚本操作 Chrome，启动 Chrome 时添加参数 `--headless` 即可

与 Chrome 开发者工具一样基于 CDP(Chrome DevTool Protocol) 协议

Puppeteer（音译：帕破替尔） 提供了一系列 API，通过 CDP 协议控制 Chromium 浏览器的行为

- UI 自动化测试，模拟表单提交，键盘输入，点击等行为
- 测试 Chrome 扩展程序
- 网页截图或者生成 PDF
- 爬取 SPA 或 SSR 网站
- 捕获网站的时间线，帮助诊断性能问题
- ...
