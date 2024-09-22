# Tailwind CSS

<https://tailwindcss.com/>

- 安装

```shell
npm i -D tailwindcss
npx tailwindcss init
```

- 配置模版：`tailwind.config.js`

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
    content: ["./src/**/*.{html,js}", "./index.html"],
    theme: {
        extend: {},
    },
    plugins: [],
}
```

- `src/input.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

- 构建

```shell
npx tailwindcss -i ./src/input.css -o ./src/output.css --watch
```

- 使用

```html
<!doctype html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="./src/output.css" rel="stylesheet">
</head>
<body>
    <h1 class="text-3xl font-bold underline">
        Hello world!
    </h1>
</body>
</html>
```

## Django-Tailwind

官方文档：<https://django-tailwind.readthedocs.io/en/latest/installation.html>

> [视频教程](https://www.youtube.com/watch?v=REnumA4TNu4&ab_channel=BugBytes)

- 安装

```shell
pip install django-tailwind

# 安装 django-browser-reload，使页面可以自动重新加载，方便调试
pip install 'django-tailwind[reload]'
```

- 配置 tailwind app

```python
INSTALLED_APPS = [
    # ...
    'tailwind',
    # ...
]
```

- 配置 django_browser_reload app

```python
INSTALLED_APPS = [
    # ...
    'django_browser_reload',
    # ...
]

MIDDLEWARE = [
    # ...
    # 应该放在 GZipMiddleware 等会对响应进行编码的中间件之后
    "django_browser_reload.middleware.BrowserReloadMiddleware",
    # ...
]
```

```python
# root urls.py
from django.urls import include, path
urlpatterns = [
    # ...
    path("__reload__/", include("django_browser_reload.urls")),
]
```

- 初始化

`python manage.py tailwind init`

执行命令后，命令行会提示定义一个应用名，默认为 theme

然后会生成一个 theme app

```text
theme/
    apps.py
    templates/
        base.html  可删除
    static_src/
        package.json
        postcss.config.js
        tailwind.config.js
        src/
            styles.css
        node_modules/  执行完后面的安装依赖命令后才会有
```

附带了一个基础模版 `theme/templates/base.html`，如果不需要可以删除

在个人应用的 base.html 模版中，只需添加如下模版标签即可使用 Tailwind

```html
<!-- base.html -->
{% load static tailwind_tags %}
...
<head>
   ...
   {% tailwind_css %}
   ...
</head>
```

- 配置 theme app

```python
INSTALLED_APPS = [
    # ...
    'theme',  # 如果没有删除 theme 的 base.html 可能会覆盖后面应用的 base.html
    # ...
]

TAILWIND_APP_NAME = 'theme'
```

- 安装 Tailwind 依赖

`python manage.py tailwind install`

这一步需要系统已安装 Node.js，如果已安装但提示找不到 npm，可以将路径手动配置到设置中

```python
# Linux/Mac: which npm
NPM_BIN_PATH = '/usr/local/bin/npm'
# Windows: where npm
NPM_BIN_PATH = r"C:\Program Files\nodejs\npm.cmd"
```

- 运行 Tailwind

```shell
# 开发环境
python manage.py tailwind start

# 生产环境构建，压缩优化
python manage.py tailwind build

# 使用最新版本 Tailwind CSS
python manage.py tailwind update  # 将运行 npm update
```

## 基于 Tailwind CSS 的组件库

和 [Bootstrap](https://getbootstrap.com/) 这类直接提供组件的框架相比，Tailwind CSS 介于组件和原始 CSS 之间，它适合需要自定义 CSS、希望能有自己风格的网站。

但目前也有很多基于 Tailwind CSS 的预定义组件库

### [Flowbite](https://flowbite.com/docs)

### [Shadcn/UI](https://ui.shadcn.com/)

基于 Radix UI 和 Tailwind CSS 的组件库，使用 TypeScript 编写，主要用于 React 框架，需要通过 JSX 来使用，风格偏商业。

AI 应用程序 <https://v0.dev/chat> 可生成由 Shadcn/UI 组成的 JSX 代码。

### [Daisyui](https://daisyui.com/)

```shell
# 先切换到项目目录，比如 cd theme/static_src
npm i -D daisyui@latest
```

```js
// 追加到 tailwind.config.js
module.exports = {
  //...
  plugins: [
    require('daisyui'),
  ],
}
```
