# [ZuoRight](http://zuoright.com)

## VS Code

### Settings Sync

- `Shift` + `Alt/Opt` + `U`(上传) 或 `D`(下载)

首次上传/下载，会弹出配置，按提示填入token和gist

### PicGo

- 从剪切板上传：`Cmd`+`Opt`+`U`（Windows：`Ctrl`+`Alt`+`U`）
- 从文件夹上传：同上+`E`
- 输入路径上传：同上+`O`

## [MKDocs](https://www.mkdocs.org/)

```shell
pip install mkdocs
pip install -U mkdocs

mkdocs --help

mkdocs new xxx
cd xxx

mkdocs serve  # 启动本地服务
mkdocs build [--clean]  # 更新构建
```

配置文件：`mkdocs.yml`

- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)

```shell
pip install mkdocs-material
# 查看版本
pip show mkdocs-material
# 升级
pip install --upgrade mkdocs-material
```

- 文章底部显示更新时间的插件

`pip install mkdocs-git-revision-date-localized-plugin`

```yaml
plugins:
  - git-revision-date-localized:  # 文章底部显示更新时间，需要先安装插件
      type: iso_date
```
