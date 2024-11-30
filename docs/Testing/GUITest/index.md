# 引言

端到端 E2E, End-to-End 测试，通过模拟真实用户场景和工作流程来验证整个应用程序是否按预期工作

不仅仅测试应用的单个功能或组件，而是测试整个应用的工作流程，这包括用户界面、网络通信、数据库操作、后端服务等。

- UFT(QTP)
- Selenium
- Appium
- Cypress

## CDP

关于开发者工具 [Chrome DevTools](https://developer.chrome.com/docs/devtools/overview?hl=zh-cn) 使用的系列教程

- <https://teobler.com/posts/20200304-devtool-editing-and-debugging>
- <https://teobler.com/posts/20200304-devtool-editing-and-debugging>

[CDP, Chrome DevTool Protocol](https://chromedevtools.github.io/devtools-protocol/) 是 Chrome DevTools 的底层协议

可通过 CDP 协议与浏览器通信：`ws://localhost:9222/devtools/browser/<id>`

> [Using CDP](https://github.com/aslushnikov/getting-started-with-cdp/blob/master/README.md)

Puppeteer, Playwright, ChromeDriver, Cypress 等工具或框架底层都是通过 CDP 操控浏览器的，更多工具可参考：[Awesome Chrome DevTools](https://github.com/ChromeDevTools/awesome-chrome-devtools#chrome-devtools-protocol)

## Headless

Headless 是指在无界面的环境中通过命令行或脚本操作 Chrome 等浏览器

比较早的工具是 PhantomJS，基于 WebKit，现已停止维护

### Puppeteer

<https://pptr.dev/>

Chrome 开发团队在 2017 年发布的一个 Node.js 包，用来模拟 Chrome 浏览器的运行，启动 Chrome 时添加参数 `--headless` 即可

Puppeteer（音译：帕破替尔）提供了一系列 API，基于 CDP 协议控制 Chromium 浏览器的行为

- UI 自动化测试，模拟表单提交，键盘输入，点击等行为
- 测试 Chrome 扩展程序
- 网页截图或者生成 PDF
- 爬取 SPA 或 SSR 网站
- 捕获网站的时间线，帮助诊断性能问题
- ...

主要是为 Chromium 内核的浏览器和 Node.js 环境设计的

```shell
npm i puppeteer  # Downloads compatible Chrome during installation.
npm i puppeteer-core  # Alternatively, install as a library, without downloading Chrome.
```

### Playwright

由 Microsoft 发布的 Node 库，与 Puppeteer 类似，但支持所有现代浏览器引擎，包括 Chrome、Firefox 和 WebKit。

<https://playwright.dev/docs/intro>

```shell
npm init playwright@latest

npx playwright test
npx playwright test --ui  # UI 模式下运行

npx playwright show-report
```

## Cypress

<https://www.cypress.io/>

Cypress 直接在浏览器中运行，消除了网络延迟和协议转换的开销

可以直接访问和操作应用的 DOM 结构以及 JavaScript 运行环境，而不是通过外部驱动，能监听应用中的各种事件，并根据事件结果动态调整测试流程。

还能够控制应用的所有网络请求，拦截和修改网络请求和响应，方便测试中模拟各种网络场景。

允许开发者编写断言来验证应用的状态。这些断言直接在浏览器中评估，提供即时的反馈。

在测试执行过程中，Cypress 可以自动捕捉屏幕截图和视频记录。

## WebdriverIO

<https://github.com/webdriverio/webdriverio>

用于浏览器中的端到端以及单元和组件测试，允许您基于 WebDriver 和 WebDriver BiDi 以及 Appium 自动化技术运行测试。

待研究

## Drissionpage

<https://drissionpage.cn/>

一个结合了 UI 操作和 Requests 请求的库

UI 操作部分基于 CDP 协议自定义了一套简化版的定位方法，并且兼容 Webdriver 的定位操作，此外还有各种人性化小功能

看着有点意思，待进一步研究

![20240721125326](https://image.zuoright.com/20240721125326.png)
