# 引言

## W3C标准

万维网（World Wide Web）是由很多链接在一起的文档和资源构成的，为此万维网联盟制定了一系列标准

- 结构标准语言：XML、HTML
- 行为标准语言：DOM、ECMAScript
- 表现标准语言：CSS

## 标记语言

标记语言（Markup Languages）是一种将文本与元数据结合的编码方式，用于定义文档的结构、样式、内容等。它们广泛应用于网页开发、文档创建、数据交换等领域。

- SGML

Standard Generalized Markup Language 标准通用标记语言

SGML 提供了一套广泛的规则，用于定义文档的结构和内容，使其能够描述几乎任何类型的文档数据，允许用户定义自己的标记语言。

核心概念：标记、元素、属性和文档类型定义（DTD）

DTD 用于规定标记语言的规则，这样浏览器才能正确地呈现内容

- XML

可扩展标记语言，SGML 的简化版本，规则更严格，旨在保持 SGML 的强大能力，同时简化其复杂性，促进不同系统和平台之间的数据交换和共享。

- HTML4.01

基于 SGML，旨在改进和扩展 HTML，以支持更多的 Web 设计需求和提高网页的可访问性和国际化。

由于 HTML4.01 基于 SGML，所以必须使用 DTD

```html
<!-- 严格模式 Strict -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

<!-- 过渡模式 Transitional -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<!-- 框架模式 Frameset -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
```

- XHTML

基于 XML 的可扩展超文本标记语言，旨在替代 HTML4.01，目的是增强 Web 文档的可移植性和互操作性，但语法规则严格，比如标签的小写和必须闭合，所以较难用。

- HTML5

HTML 最新和最广泛使用的标准，基于 HTML 和 XHTML1.0，兼容但不基于 SGML 和 XML，可以不使用 DTD，引入了许多新的标签属性和功能，以支持更复杂的 Web应用程序和更丰富的媒体内容。

- SVG

Scalable Vector Graphics，用于描述二维图形和图形应用程序的 XML 标记语言。支持动画和交互性，可直接嵌入 HTML 网页。

- RSS

Really Simple Syndication or Rich Site Summary，一种用于发布频繁更新的内容（如博客条目、新闻头条）的 XML 格式。允许用户订阅内容，自动接收更新。

- Markdown

一种轻量级标记语言，使用简单的标记语法实现文档格式化。广泛用于撰写说明文档、论坛、博客等。

- LaTeX

一种基于 TeX 的排版系统，用于生成高质量的科技和数学文档。通过标记命令控制文档的结构和样式。

## 架构

- `MVC`: Modle-View-Controller 经典Web应用架构
- `MVP`: Model-View-Presenter 常用于Android和.NET开发
- `MVVM`: Model-View-ViewModel 常见于Vue、AngularJS等框架
- `MVT`: Model-View-Template 常用于Django

不同架构的区别在于用户界面、业务逻辑、数据层之间如何交互

MVC 和 MVP 强调了控制器/呈现器作为中介的角色，Controller 作为流量控制器，负责协调 Model 和 View。View 可以更主动地从 Model 中获取数据，而 Controller 更多地关注于更新 Model 和选择 View，Presenter 对 View 有更直接的控制，而且业务逻辑几乎全部在 Presenter 中处理。

MVVM 则通过双向数据绑定将视图和模型分离

MVT 则提供了一个清晰的模板层来处理数据展示，需要注意的是，MVT 中的 View 是逻辑控制层，Template 才是类似其它架构中的 View
