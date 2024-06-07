# [ZuoRight](http://zuoright.com)

> `VSCode` + `MarkDown` + `七牛云图床` + `Git` + `Github`(`Pages`, `Actions`, `Discussions`)

## MKDocs

> [官方文档](https://www.mkdocs.org/)
>
> [推荐主题：Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)

```shell
# 直接安装
pip install mkdocs

# 安装Material for MkDocs主题时会附带安装Mkdocs
pip install mkdocs-material

# 安装文章底部显示更新时间的插件
pip install mkdocs-git-revision-date-localized-plugin
```

```shell
# 初始化
mkdocs new .
<<'COMMENT'
├─ docs/
│  └─ index.md
└─ mkdocs.yml

最小配置
theme:
  name: material
  custom_dir: overrides  # 扩展主题
COMMENT

mkdocs --help

# 启动本地服务预览
mkdocs serve

# 手动将docs目录中的markdown构建成html等静态文件
mkdocs build

# 新建gh-deploy分支，自动构建并推送到origin
# mkdocs gh-deploy
```

定制化修改主题：<https://squidfunk.github.io/mkdocs-material/customization/#extending-the-theme>

## GitHub Pages

> 官方文档：<https://docs.github.com/cn/pages>

- 解析域名

![20210725115345](http://image.zuoright.com/20210725115345.png)

- 设置自定义域名

![20210725120036](http://image.zuoright.com/20210725120036.png)

- 访问测试

![20210725122020](http://image.zuoright.com/20210725122020.png)

## 七牛云图床

更换证书，修改HTTPS配置：<https://portal.qiniu.com/cdn/domain>

## Markdown

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
