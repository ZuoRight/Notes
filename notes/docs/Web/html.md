# HTML

[HTML免费教程](https://wangdoc.com/html/intro.html){ .md-button .md-button--primary }

```html
<form method="post">
    <input type="text" name="title" autocomplete="off" required>
    <input type="submit" name="submit" value="Add" class="btn">
</form>

<form method="post">
    <label for="name">名字</label>
    <input type="text" name="name" id="name" autocomplete="off"><br>

    <label for="occupation">职业</label>
    <input type="text" name="occupation" id="occupation"><br>

    <input type="submit" name="submit" value="登录">
</form>
```

## 元信息

### <!DOCTYPE>声明

必须是文档的第一行，不是HTML标签，作用是告诉浏览器是哪个版本的标签

- HTML4.01

```html
<!-- 声明模式 -->
严格模式 Strict
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

过渡模式 Transitional
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

框架模式 Frameset
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">

DTD：文档类型定义
在XML中，DTD可定义合法的XML文档构建模块，它使用一系列合法的元素来定义文档的结构
在HTML中，DTD规定了标记语言的规则，这样浏览器才能正确地呈现内容
```

- HTML5

HTML5不基于SGML，所以不需要引用DTD

```html
<!-- 声明模式 -->
<!DOCTYPE html>
```

### icon

制作一个`favicon.ico`格式图片，像素大小为16*16，所使用的颜色不得超过16色，然后放到网站的根目录下

```html
<!-- 标题栏 -->
<link rel="icon" href="ico地址" type="image/x-icon">

<!-- 收藏夹 -->
<link rel="shortcut icon" href="ico地址" type="image/x-icon">
```

## 嵌入JS

一般比较简单的脚本可以直接嵌入到HTML中，可以插在HTML文档的任何位置，当浏览器遇到`<script>`标签，代码会自动运行

但通常会存放到单独的文件中引入，浏览器会通过链接下载它，然后保存到缓存

```html
<!DOCTYPE HTML>
<html>
    <body>
        <script> alert('Hello, world!'); </script>
        <!-- 外部引入 -->
        <script src="/path/to/script.js"> alert("当使用src属性时，此处的代码会被忽略"); </script>
    </body>
</html>
```

## 提交表单

```html
<form id="test">
    <input type="text" name="test">
    <button type="button" onclick="doSubmitForm()">Submit</button>
</form>

<script>
function doSubmitForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input
    form.submit();  // js提交form，但通常不建议这样
    return true;  // 而是返回true，用form自己的onsubmit方法提交
}
</script>
```
