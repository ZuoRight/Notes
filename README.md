# [ZuoRight](https://zuoright.com)

> `VSCode` + `MarkDown` + `七牛云图床` + `Git` + `Github`(`Pages`, `Actions`, `Discussions`)

## MKDocs

[官方文档](https://www.mkdocs.org/)

推荐主题：[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)

```shell
# 直接安装
pip install mkdocs

# 安装 Material for MkDocs 主题时会附带安装 Mkdocs
pip install mkdocs-material

# 安装插件
pip install mkdocs-glightbox  # 放大图片
pip install mkdocs-awesome-pages-plugin  # 定义目录顺序
pip install mkdocs-git-revision-date-localized-plugin  # 文章底部显示更新时间
```

```shell
# 初始化
mkdocs new .

# 目录结构
'
├─ docs/
│  └─ index.md
└─ mkdocs.yml
'

# 最小配置
'
theme:
  name: material
  custom_dir: overrides  # 扩展主题
'

mkdocs --help

# 启动本地服务预览
mkdocs serve  # -a localhost:8000

# 手动将 docs 目录中的 md 构建成 html 等静态文件
mkdocs build

# 自动构建并推送到 gh-deploy 分支
mkdocs gh-deploy
```

定制化修改主题：<https://squidfunk.github.io/mkdocs-material/customization/#extending-the-theme>

## 评论

配置：<https://giscus.app>

```plaintext
---
comments: true  开启评论
---
```

## 七牛云图床

密钥管理：<https://portal.qiniu.com/user/key>

更换证书，[修改 HTTPS 配置](https://portal.qiniu.com/cdn/domain)，勾选「同意七牛云代申请免费证书」即可，有效期 3 个月，证书虽然免费，但 HTTPS 服务是按量计费的。

### PicGo 上传

- 从剪切板上传：`cmd` + `opt` + `u`
- 从文件夹上传：`cmd` + `opt` + `e`
- 输入路径上传：`cmd` + `opt` + `o`

VSCode 安装 [PicGo](https://picgo.github.io/PicGo-Core-Doc/zh/guide/) 插件，配置七牛云密钥

![20210808174641](https://image.zuoright.com/20210808174641.png)

## 备案

阿里云备案信息核查存在问题：已备案域名未使用阿里云中国内地节点服务器

解决方式：添加 A 记录，将二级域名，比如 <http://aliyun.zuoright.com> 解析到阿里云的某个服务器，比如 `39.100.0.23`
