# 引言

## 方式1

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

## 方式2：AJAX

Web的运作原理：一次HTTP请求对应一个页面。

如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用JavaScript发送这个新请求，接收到数据后，再用JavaScript更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

AJAX全称：Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求，在现代浏览器上写AJAX主要依靠XMLHttpRequest对象。

## 方式3：Promise

Promise有各种开源实现，比如axios
