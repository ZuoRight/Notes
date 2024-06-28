# 标记语言

标记语言（Markup Languages）是一种将文本与元数据结合的编码方式，用于定义文档的结构、样式、内容等。

它们广泛应用于网页开发、文档创建、数据交换等领域。

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

## Markdown

一种轻量级标记语言，使用简单的标记语法实现文档格式化。广泛用于撰写说明文档、论坛、博客等。

Markdown 语法，一般可分为原生语法和衍生语法

### 原生语法

是由 John Gruber 于 2004 年创造，此后貌似再也没有更新过，但足以满足大部分写作。

> <https://daringfireball.net/projects/markdown/>

主要由以下两部分构成

- 区块元素：标题、引用、列表(分为有序和无序)、区块代码、分隔线等
- 行内元素：强调(包含加粗和斜体)、行内代码、链接、图片等

### 衍生语法

为了补充原生语法所不具备的功能，一些组织或者个人对其进行了扩展，即所谓的衍生语法，主要有：

- [GFM(Github Flavored Markdown)](https://help.github.com/en/github/writing-on-github
)：支持任务列表、表格、围栏式区块代码、删除线等
- [PHP Markdown Extra](https://michelf.ca/projects/php-markdown/extra)：支持锚点链接、注脚、缩略语等
- [MultiMarkdown](https://fletcherpenney.net)：支持上标与下标、行内和区块公式、以及更复杂的表格等

### 其它语法

我们使用过的各种本地以及在线编辑器可能还创造了属于自己的语法，皆归为其它

## LaTeX

一种基于 TeX 的排版系统，用于生成高质量的科技和数学文档。通过标记命令控制文档的结构和样式。
