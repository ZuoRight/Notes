# SEO

设置网站 logo 的同时，保留 h1 的文字，便于搜索引擎理解网页内容，但可以把文字移出视口实现文本隐藏但不影响 SEO 的目的。

```html
<head>
    <style>
        header {
            background-color: #231C1A;
            width: 100%;
            height: 80px;
        }
        h1 {
            color: white;
            position: absolute;
            left: 120px;
            top: 0px;
            width: 210px;  /* 设置宽度 */
            line-height: 80px;  /* 垂直居中 */
            text-indent: -9999px;  /* 通过负值缩进，将文本内容移出视口，隐藏文本 */
            background-image: url('images/logo.svg');  /* 设置背景图像 */
            background-repeat: no-repeat;  /* 不重复背景图像 */
            background-position: center;  /* 背景图像居中 */
        }
    </style>
</head>
<body>
    <header>
        <h1>网站名称</h1>
    </header>
</body>
```
