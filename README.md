# [ZuoRight](http://zuoright.com)

## VS Code

### Settings Sync

- `Shift` + `Alt/Opt` + `U`(上传) 或 `D`(下载)

首次上传/下载，会弹出配置，按提示填入token和gist

### [PicGo](https://picgo.github.io/PicGo-Core-Doc/zh/guide/)

- 从剪切板上传：`Cmd`+`Opt`+`U`（Windows：`Ctrl`+`Alt`+`U`）
- 从文件夹上传：同上+`E`
- 输入路径上传：同上+`O`

## [MKDocs](https://www.mkdocs.org/)

`pip install mkdocs`

> 配置文件：mkdocs.yml

- [主题：Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)

`pip install mkdocs-material`

```yaml
theme:
  name: material
```

- 插件：文章底部显示更新时间

`pip install mkdocs-git-revision-date-localized-plugin`

```yaml
plugins:
  - git-revision-date-localized:  # 文章底部显示更新时间，需要先安装插件
      type: iso_date
```

```shell
mkdocs --help
mkdocs new xxx
cd xxx

mkdocs serve  # 启动本地服务
mkdocs build  # 更新构建
```
