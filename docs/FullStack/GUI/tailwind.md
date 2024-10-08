# Tailwind CSS

官网：<https://tailwindcss.com/>

Playground：<https://play.tailwindcss.com/>

使用 Tailwind，可以直接在 HTML 中应用预先存在的实用程序类（Utility Classes）来设置元素样式，这种方法使我们能够实现完全自定义的组件设计，而无需编写自定义的 CSS。

相比于内联样式（Inline Styles），预定义的设计系统使得设计更加统一，支持响应式布局，设计更简单

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
        <div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex items-center space-x-4">
            <div class="shrink-0">
                <img class="size-12" src="/img/logo.svg" alt="ChitChat Logo">
            </div>
            <div>
                <div class="text-xl font-medium text-black">ChitChat</div>
                <p class="text-slate-500">You have a new message!</p>
            </div>
        </div>
    </body>
</html>
```

## 状态修饰符

<https://tailwindcss.com/docs/hover-focus-and-other-states>

每个实用程序类都可以通过在类名的开头添加一个修饰符，来精确控制 `hover`, `focus` 等状态时的样式，而无需离开 HTML

- 伪类：`:hover`, `:focus`, `:first-child`, `:required`
- 伪元素：`::before`, `::after`, `::placeholder`，`::selection`
- 属性选择器：`[dir="rtl"]`, `[open]`
- 媒体和功能查询：响应式断点、深色模式、`prefers-reduced-motion`
- 使用任意值，比如：`min-[320px]:text-center `

比如 hover 按钮时变色

```html
<button class="bg-sky-500 hover:bg-sky-700 ...">
    Button
</button>

<!-- 组合使用：在黑暗模式下、中等断点处、悬停时更改背景颜色 -->
<button class="dark:md:hover:bg-fuchsia-600 ...">
    Button
</button>
```

传统 CSS 中要这样实现

```css
.btn-primary {
    background-color: #0ea5e9;
}
.btn-primary:hover {
    background-color: #0369a1;
}
```

## 响应式布局

<https://tailwindcss.com/docs/responsive-design>

每个实用程序类都可以在不同的断点处有条件地应用，以此构建复杂的响应式布局，而无需离开 HTML

断点(breakpoints)：`sm`(samll), `md`(medium), `lg`(large), `xl`(extra large), `2xl`(double extra large)

不同断点的尺寸是可以通过配置修改的，默认 sm 为 640px

```html
<head>
    <!--必须要有这行 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
    <div class="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
        <!-- 在中大型屏幕上 div 从默认的 display: block 变为 display: flex -->
        <div class="md:flex">
            <!-- 防止图片在中大型屏幕上缩小 -->
            <div class="md:shrink-0">
                <img class="h-48 w-full object-cover md:h-full md:w-48" src="/img/building.jpg" alt="Modern building architecture">
            </div>
            <div class="p-8">
                <div class="uppercase tracking-wide text-sm text-indigo-500 font-semibold">Company retreats</div>
                <a href="#" class="block mt-1 text-lg leading-tight font-medium text-black hover:underline">Incredible accommodation for your team</a>
                <p class="mt-2 text-slate-500">Looking to take your team away on a retreat to enjoy awesome food and take in some sunshine? We have a list of places to do just that.</p>
            </div>
        </div>
    </div>
</body>
```

默认情况下，Tailwind 与 Bootstap 等框架一样，使用移动优先断点系统，即不带前缀时，对所有屏幕都有效，带前缀时仅对断点及以上屏幕有效

注意：带 `sm:` 前缀时，对所有屏幕都一样，而不是在小屏幕有效的意思

如果只对移动端有效其实应该使用无前缀方式，而在较大断点处覆盖它们

```html
<!-- 对所有屏幕都居中 -->
<div class="sm:text-center"></div>

<!-- 640px以下居中，640px以上左对齐 -->
<div class="text-center sm:text-left"></div>

<!-- 限定 md～xl 之（不包括 xl） -->
<div class="md:max-xl:flex">
<!-- 只在单个断点 md 生效 -->
<div class="md:max-lg:flex">
```

- 间隙：`gap-*`, `gap-x-*`, `gap-y-*`
- 控制 items 在 flex 和 grid 容器中如何沿主轴定位：`justify-xxx`
- 控制 rows 在多行 flex 和 grid 容器中定位：`content-xxx`
- 控制 content 的定位（同时控制 items 和 rows）：`place-content-xxx`

## 容器

Tailwind 的容器不会自动居中，也没有任何内置的水平填充。（可以配置为默认）

`container` 类设置元素的 max-width 以匹配当前断点的 min-width，响应式：`md:container`

```html
<div class="container mx-auto px-4">
    <!-- ... -->
</div>
```

填充 [Padding](https://tailwindcss.com/docs/padding)

- 所有边：`p-*`
- 水平（padding-left & right）：`px-*`
- 垂直（padding-top & bottom）：`py-*`

边距 [Margin](https://tailwindcss.com/docs/margin)

- 所有边：`m-*`
- 水平（margin-left & right）：`mx-*`，水平居中：`mx-auto`
- 垂直（margin-top & bottom）：`my-*`，垂直居中：`my-auto`

尺寸

- 宽：`w-*`，最小宽：`min-w-*`，最大宽：`max-w-*`
- 高：`h-*`，最小高：`min-h-*`，最大高：`max-h-*`
- 同时设置宽高：`size-*`

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
