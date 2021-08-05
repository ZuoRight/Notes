# [ZuoRight](http://zuoright.com)

## MKDocs

> [官方文档](https://www.mkdocs.org/)
>
> [推荐主题：Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)

```shell
# 安装Material for MkDocs主题时会自动安装Mkdocs
# 如果不想用此主题可单独安装Mkdocs：pip install mkdocs
pip install mkdocs-material

# 安装文章底部显示更新时间的插件
pip install mkdocs-git-revision-date-localized-plugin
```

> 创建docs目录存放markdown源文件
>
> 以及配置文件：`mkdocs.yml`

```yaml
theme:
  name: material

plugins:
  - git-revision-date-localized:  # 文章底部显示更新时间
      type: iso_date
```

```shell
mkdocs --help

# 切换到配置文件路径
# 启动本地服务，预览
mkdocs serve

# 手动将docs目录中的markdown构建成html等静态文件
mkdocs build

# 新建gh-deploy分支，自动构建并推送到origin
mkdocs gh-deploy
```

## GitHub Pages

> 官方文档：<https://docs.github.com/cn/pages>

- 解析域名

![20210725115345](http://image.zuoright.com/20210725115345.png)

- 设置自定义域名

![20210725120036](http://image.zuoright.com/20210725120036.png)

- 访问测试

![20210725122020](http://image.zuoright.com/20210725122020.png)

## [GitHub Actions](.github/workflows)

```yaml
name: ci bolg test
on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - uses: actions/setup-python@v2
        with:
          python-version: 3.x

      - run: |
          pip install mkdocs-material mkdocs-git-revision-date-localized-plugin
          
      - run: |
          git config user.name github-actions
          git config user.email github-actions@github.com
          
      - run: |
          cd notes
          mkdocs build
          
      - run: |
          git add .
          git rm -r --cached notes/docs
          git rm -r --cached .github/workflows
          git commit -m "ci commit"
          git push origin master:dev --force

```
