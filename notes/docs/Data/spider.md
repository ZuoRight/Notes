# 数据采集

- 开源数据源
- 爬虫
- 日志
- 传感器

## 爬虫

- 工具：八爪鱼、火车采集器、搜集客等
- 自建：Beautiful Soup4、Requests-HTML
- 框架：Pyspider、Scrapy

### 解析库

- Requests-HTML

<https://requests-html.kennethreitz.org/>

Requests 作者创建的 HTML Parsing for Humans，集成了 Requests、lxml、html5lib、PyQuery 等

- Beautiful Soup4

<https://www.crummy.com/software/BeautifulSoup/>

用于解析、提取和修改 HTML、XML 等静态网页文档的数据，轻量且高效，需要搭配 Requests 发起请求，依赖 lxml、html5lib 解析库，涉及 JS 渲染需要借助 Selenium 等。

- lxml

唯一支持 XML 的解析器，速度快，容错能力强

- html5lib

以浏览器方式解析文档，生成 H5 格式，容错性最好，不依赖外部扩展，但速度慢

- PyQuery

仿照 jQuery 的 Python 实现，语法与 jQuery 几乎完全相同


