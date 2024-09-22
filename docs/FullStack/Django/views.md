# Views

在 Django 中，视图 `views.py` 是用于处理用户请求并返回响应的函数或类。

视图的主要任务是接收 HTTP 请求、通过调用模型处理数据、并返回适当的 HTTP 响应，可能是 HTML、重定向、JSON 数据等任何内容。

- 普通视图（Function-Based Views, FBVs）使用函数来定义，更灵活
- 通用视图（Class-Based Views, CBVs）使用类来定义，适用于许多常见的 CRUD 操作和标准用例。

实际项目中，通常可以同时使用这两种类型的视图，以满足不同的需求

## 请求

<https://docs.djangoproject.com/zh-hans/5.1/ref/request-response/>

当一个页面被请求时，Django 会创建一个 HttpRequest 对象，这个对象包含了请求的元数据，然后 Django 加载相应的视图，将 HttpRequest 作为视图函数的第一个参数，通常名为 `request`

每个视图都要负责实例化、填充和返回一个 HttpResponse 对象

```python
from django.shortcuts import render  # 引入快捷函数
from .models import Question  # 引入模型

# 视图函数的名称没有要求，可随意
def index(request):
    try:
        xxx = request.POST["key_name"]
    except (KeyError, Choice.DoesNotExist):
        pass
    else:
        pass
    return render(request, 'hello_world.html', context)
```

### HttpRequest

- request.scheme 请求协议，比如 `http` 或 `https`
- request.path 完整路径的字符串，比如 `"/music/bands/the_beatles/"`
- request.method 请求方法比如 `GET` 或 `POST`
- request.content_type 请求头 CONTENT_TYPE 对应的 MIME 类型的字符串
- request.body 获取非常规表单数据，比如二进制图像，XML 有效负载等
- request.headers 
- request.META
- request.COOKIES 一个包含所有 cookies 的字典，键和值是字符串
- request.GET 类似字典的对象，包含所有给定的 HTTP GET 参数
- request.POST 类似字典的对象，包含所有给定的 HTTP POST 参数，取到的值永远是字符串，取不到值会报 KeyError
- request.FILES 类似字典的对象，包含所有上传的文件
- request.user 代表当前登录用户，`request.user.is_authenticated`
- request.is_secure() 如果请求是通过 HTTPS 发出的，则返回 True

### 视图装饰器

```python
from django.views.decorators.http import require_http_methods

# 要求视图只接受特定的请求方法，请求方法需要大写
@require_http_methods(["GET", "POST"])
def my_view(request):
    pass

# 其它
@require_GET()  # 要求视图只接受 GET 方法
@require_POST()  # 要求视图只接受 POST 方法
@require_safe()  # 要求视图只接收 GET 和 HEAD 方法
```

### QueryDict

在一个 HttpRequest 对象中， GET 和 POST 属性是 `django.http.QueryDict` 的实例，QueryDict 是字典的一个子类

```python
from django.http import QueryDict

q = QueryDict("a=1&a=2&c=3")  # <QueryDict: {'a': ['1', '2'], 'c': ['3']}>
q.lists()  # [('a', ['1', '2'])]
```

## 返回

### HttpResponse

`response = HttpResponse()`

属性

- response.status_code 状态码
- response.cookies
- response.headers 响应头

子类

- 200 HttpResponse
- 301 HttpResponseRedirect
- 403 HttpResponseForbidden
- 404 HttpResponseNotFound
- 500 HttpResponseServerError

没有子类的状态码可以这样返回：HttpResponse(status=201)

- 设置头字段

```python
response.headers["Age"] = 120  # 方式1
response["Age"] = 120  # 方式2

# 删除
del response.headers["Age"]
del response["Age"]  # 不存在不会引发 KeyError
```

- 返回文本

```python
from django.http import HttpResponse

def index(request):
    x = "Hello, World"
    return HttpResponse(x)
```

- 将响应视为文件附件

```python
# 返回Excel表
response = HttpResponse(
    my_data,
    headers={
        "Content-Type": "application/vnd.ms-excel",
        "Content-Disposition": 'attachment; filename="foo.xls"',
    },
)
```

### 返回模板

- loader

```python
from django.http import HttpResponse
from django.template import loader

def index(request):
    # 上下文的value将被渲染到模版中对应的key：{{ key }}
    context = {"key": "Hello, World"}

    template = loader.get_template('hello_world.html')
    return HttpResponse(template.render(context, request))
```

- 便捷函数 render

<https://docs.djangoproject.com/zh-hans/5.1/topics/http/shortcuts/#render>

将给定的模板与上下文字典组合在一起，并以渲染的文本返回一个 HttpResponse 对象

```python
from django.shortcuts import render

def index(request):
    context = {"key": "Hello, World"}
    return render(request, 'hello_world.html', context)
```

### 重定向

- HttpResponseRedirect

```python
from django.http import HttpResponseRedirect

def my_view(request):
    ...
    return HttpResponseRedirect('/some/url/')
```

- 便捷函数 redirect

返回临时重定向，加 `permanent=True` 则永久重定向

```python
from django.shortcuts import redirect

def my_view(request):
    ...
    # 参数可以是多种形式
    return redirect("https://example.com/")  # 完整URL
    return redirect("/some/url/")  # 硬编码URL
    return redirect("some-view-name", foo="bar")  # 视图函数和其参数，会被 reverse() 反向解析
    return redirect(MyModel.objects.get(...))  # 模型，将调用模型的 get_absolute_url() 函数
```

reverse('one_app:results', args=(question.id,))
# 参数视图函数中避免URL硬编码：reverse(视图名字，args=(视图所需参数的元组,))

### 返回 404 页面

- HttpResponseNotFound

需要定义错误页面的 HTML，可以创建名为 `404.html` 的 HTML 模板，并放置在模版路径顶层

```python
return HttpResponseNotFound("<h1>Page not found</h1>")
```

- Http404

返回标准的错误页面

```python
from django.http import Http404

def my_view(request):
    try:
        obj = MyModel.objects.get(pk=1)
    except MyModel.DoesNotExist:
        raise Http404("No MyModel matches the given query.")
```

- get_object_or_404

在给定的模型管理器上调用 get()，会引发 Http404

```python
from django.shortcuts import get_object_or_404

def my_view(request):
    # 等价上面 Http404 的写法
    obj = get_object_or_404(MyModel, pk=1)  # 传递 Model

    # 也可以传递 QuerySet
    """
    queryset = Book.objects.filter(title__startswith="M")
    get_object_or_404(queryset, pk=1)
    """
```

- get_list_or_404()

```python
from django.shortcuts import get_list_or_404

def my_view(request):
    my_objects = get_list_or_404(MyModel, published=True)

# 等价于
from django.http import Http404
def my_view(request):
    # 将模型管理器.filter()转换为列表，如果为空，则引发 Http404
    my_objects = list(MyModel.objects.filter(published=True))
    if not my_objects:
        raise Http404("No MyModel matches the given query.")
```

## 通用视图

<https://docs.djangoproject.com/zh-hans/5.1/topics/class-based-views/generic-display/>

基于类的通用视图，是为了缓解单调重复的视图层工作

通用视图均继承自 View 类，它处理视图链接到 URLs，HTTP 方法调度和其他简单功能

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render

from django.views import View
# from django.views.generic import TemplateView, RedirectView

from .forms import MyForm

# 基于函数的视图，使用条件分支来响应不同的请求方法
def myview(request):
    if request.method == "POST":
        form = MyForm(request.POST)
        if form.is_valid():
            # <process form cleaned data>
            return HttpResponseRedirect("/success/")
    else:
        form = MyForm(initial={"key": "value"})

    return render(request, "form_template.html", {"form": form})


# 基于类的视图，可以使用不同的类实例方法响应不同 HTTP 请求方法
class MyFormView(View):
    form_class = MyForm
    initial = {"key": "value"}
    template_name = "form_template.html"

    def get(self, request, *args, **kwargs):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {"form": form})

    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)
        if form.is_valid():
            # <process form cleaned data>
            return HttpResponseRedirect("/success/")
        return render(request, self.template_name, {"form": form})
```

扩展通用视图，即覆盖类的属性或方法

```python
# 父类
class GreetingView(View):
    greeting = "Good Day"
    def get(self, request):
        return HttpResponse(self.greeting)

# 子类可以覆盖父类的属性和方法
class MorningGreetingView(GreetingView):
    greeting = "Morning to ya"

# URLconf 通过给 as_view 传递参数也可以覆盖类属性
urlpatterns = [
    path("about/", GreetingView.as_view(greeting="G'day")),
]
```

### 内置通用视图

```python
# one_app/views.py
from django.views.generic import ListView, DetailView
from .models import Question


# 通用视图 ListView 会显示一个特定类型对象的列表页面
class IndexView(ListView):  # 不再需要传递 request
    model = Question  # 关联模型
    template_name = 'one_app/index.html'  # 关联模版
    context_object_name = 'latest_question_list'  # 上下文变量，默认为object_list


# 通用视图 DetailView 会显示一个特定类型对象的详细信息页面
class ResultsView(DetailView):
    model = Question
    template_name = 'one_app/results.html'
```
