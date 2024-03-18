# 框架

## Bootstrap

<https://getbootstrap.com/>

## Tailwind CSS

<https://tailwindcss.com/>

和Bootstrap这类直接提供组件的CSS框架相比，Tailwind CSS介于组件和原始CSS之间，它适合需要自定义CSS、希望能有自己风格的网站

- 安装

```shell
npm install -D tailwindcss
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
