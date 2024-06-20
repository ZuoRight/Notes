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


## 七牛云图床

密钥管理：<https://portal.qiniu.com/user/key>

更换证书，修改 HTTPS 配置：<https://portal.qiniu.com/cdn/domain>

### PicGo 上传

- 从剪切板上传：`cmd` + `opt` + `u`
- 从文件夹上传：`cmd` + `opt` + `e`
- 输入路径上传：`cmd` + `opt` + `o`

VSCode 安装 [PicGo](https://picgo.github.io/PicGo-Core-Doc/zh/guide/) 插件，配置七牛云密钥

![20210808174641](http://image.zuoright.com/20210808174641.png)
