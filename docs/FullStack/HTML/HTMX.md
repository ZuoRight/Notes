# HTMX

<https://htmx.org/>

HTMX 可以使得任何元素或事件发出 GET 和 POST 等各种请求，且可以更新任何元素，期望响应为 HTML 而不是 Json。

非常适合搭配 Django, Tailwind CSS, Alpine.js 等一起使用

- [为什么选择 HTMX](https://jimvvv.dev/posts/why-do-i-choose-htmx/)
- [Server-Side Examples](https://htmx.org/server-examples/)

## 安装

- CDN

为了保证性能、隐私和安全性，不建议在生产环境使用

> [避免使用 Javascript CDN 的原因](https://blog.wesleyac.com/posts/why-not-javascript-cdn)

```html
<script src="https://unpkg.com/htmx.org@2.0.2" integrity="sha384-Y7hw+L/jvKeWIRRkqWYfPcvVxHzVzn5REgzbawhxAuQGwX1XWe70vji+VSeHOThJ" crossorigin="anonymous"></script>
```

- 下载到本地

<https://unpkg.com/htmx.org@2.0.2/dist/htmx.min.js>

```html
<!-- 带有defer的脚本不会阻止HTML文档的解析 -->
<script src="/path/to/htmx.min.js" defer></script>
```

## 语法

核心是一组属性，允许直接从 HTML 发出 AJAX 请求

```html
<button hx-post="/clicked"  // 请求发送到哪里
    hx-trigger="click"  // 触发什么事件
    hx-target="#parent-div"  // 替换的元素
    hx-swap="outerHTML"  // 默认替换标签内部内容，也可以替换整个标签
>
    Click Me!
</button>

<!-- 可以加 data- 前缀使用 -->
<a data-hx-post="/click"> Click Me! </a>
```

### Trigger

可以使用 `hx-trigger="mouseenter"` 指定触发事件

还可以指定多个触发器，用逗号分隔

如果不指定，默认情况如下

- input, textarea, select 由 change 事件上触发
- form 由 submit 事件触发
- 其它的都由 click 事件触发

修饰符

- `once` 只触发一次：`hx-trigger="mouseenter once"`
- `changed` 仅当元素的值修改时发出请求
- `delay:<time interval>` 延迟发出请求
- `throttle:<time interval>` n秒内只发出1次请求
- `from:<CSS Selector>` 监听不同元素上的事件
- `every 2s` 轮询，间隔每2s

## django-htmx

`pip install django-htmx`

django-htmx 不包含 htmx 本身，模版中还是需要配置 htmx 的

- 配置

```python
INSTALLED_APPS = [
    ...,
    "django_htmx",
    ...,
]

MIDDLEWARE = [
    ...,
    "django_htmx.middleware.HtmxMiddleware",
    ...,
]
```

- views

```python
from django_htmx.http import HttpResponseClientRedirect, HttpResponseClientRefresh

def demo(request):
    if request.htmx:
        pass
        '''
        return HttpResponseClientRefresh()  # 刷新页面
        return HttpResponseClientRedirect("/")  # 重定向到首页
        return HttpResponseLocation("/success/", target="#id")  # 加强版重定向，比如可以重定向到某个请求，但是保持导航、侧边栏等不变
        return HttpResponseStopPolling()  # 停止轮询
        '''

def success(request):
    return HttpResponse("Congratulations!")
```
