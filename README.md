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
mkdocs gh-deploy
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
