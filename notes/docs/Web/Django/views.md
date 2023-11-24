# 视图

在 Django 中，视图 `views.py` 是用于处理用户请求并返回响应的函数或类。

视图的主要任务是接收 HTTP 请求、通过调用模型处理数据、并返回适当的 HTTP 响应，可能是 HTML 页面、重定向、JSON 数据等。

普通视图（Function-Based Views, FBVs）使用函数来定义，更灵活

通用视图（Class-Based Views, CBVs）使用类来定义，适用于许多常见的 CRUD 操作和标准用例。

实际项目中，通常可以同时使用这两种类型的视图，以满足不同的需求

## HttpRequest

```python
from django.shortcuts import render  # 引入快捷函数
from .models import Question  # 引入模型


def index(request):  # request=<HttpRequest object>
    # 通过request.POST字典对象获取表单提交的数据
    # 取不到值会报KeyError，取到的值永远是字符串
    try:
        xxx = request.POST["key_name"]
    except (KeyError, Choice.DoesNotExist):
        pass
    else:
        pass

    return render(request, 'hello_world.html', context)
```

## HttpResponse

### 返回文本

```python
from django.http import HttpResponse

def index(request):
    x = "Hello, World"
    return HttpResponse(x)
```

### 返回模板：loader

```python
from django.http import HttpResponse
from django.template import loader

def index(request):
    x = "Hello, World"
    context = {"key": x}  # 上下文

    template = loader.get_template('hello_world.html')  # 载入模板文件
    return HttpResponse(template.render(context, request))  # 上下文变量的值将被渲染到模版中的对应变量：{{ key }}
```

### 返回模版：render

```python
from django.shortcuts import render


def index(request):
    x = "Hello, World"
    context = {"key": x}
    return render(request, 'hello_world.html', context)  # 模版路径：`xx_app/templates/xx.html`
```

### 返回404

```python
from django.shortcuts import render, get_object_or_404
from django.http import Http404
from .models import Question


def detail(request, question_id):
    """方式1"""
    # try:
    #     question = Question.objects.get(pk=question_id)
    # except Question.DoesNotExist:
    #     raise Http404("Question does not exist")

    """方式2，推荐"""
    # 使用 get_object_or_404(模型对象，任意数量的关键字参数) 方式返回404
    question = get_object_or_404(Question, pk=question_id)  # 如果关键字参数查不到结果则返回404
    
    return render(request, 'one_app/detail.html', {'question': question})
```

### 重定向

```python
from django.http import HttpResponseRedirect

# 构造函数：HttpResponseRedirect(重定向后的URL)
# 视图函数中避免URL硬编码：reverse(视图名字，args=(视图所需参数的元组,))
return HttpResponseRedirect(reverse('one_app:results', args=(question.id,)))  # /one_app/3/results/
```

## 路由配置

然后在项目的 `urls.py` 中配置路由，将 views 与 项目绑定

```python
# mysite/urls.py
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    # 如果要引用其它的URLconfs，应该总是使用inlcude()这种形式
    path('one_app/', include('one_app.urls')),
]
```

然后在 app 的 `urls.py` 中配置路由，将页面 与 views 绑定

```python
# one_app/urls.py
from django.urls import path
from . import views

# 添加命名空间，避免多个应用路由名称相同混淆
app_name = "one_app"

urlpatterns = [
    # 格式：path(route, view, kwargs, name)
    """
    route：必选，路由正则
    view：必选，调用视图函数，传入HttpRequest对象
    kwargs：可选，字典的形式给视图函数传递任意个关键字
    name：可选，给路由起名字，在项目任意位置引用
    """
    path('/index', views.index, name='index'),
]
```

## 通用视图

```python
# one_app/views.py
from django.views import generic
from .models import Question

# 通用视图ListView会显示一个特定类型对象的列表页面
class IndexView(generic.ListView):
    # 通过model属性告知通用视图作用于哪个模型
    model = Question
    # 通过template_name属性告知通用视图使用哪个模版
    # 如果不指定，ListView默认使用<app name>/<model name>_list.html做模版
    template_name = 'one_app/index.html'
    # 通过context_object_name属性告知通用视图上下文变量
    # 如果不指定，默认为question_list
    context_object_name = 'latest_question_list'

# 通用视图DetailView会显示一个特定类型对象的详细信息页面
class ResultsView(generic.DetailView):  # 不再需要传递request和question_id
    model = Question
    # 如果不指定template_name，DetailView默认使用<app name>/<model name>_detail.html做模版
    template_name = 'one_app/results.html'
    # 如果不指定context_object_name，默认为question
```

- 路由配置

```python
# one_app/urls.py
from django.urls import path
from . import views

app_name = 'one_app'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    # DetailView会捕获名为"pk"的主键值，所以路由参数的变量名要改为"pk"
    # 视图参数也要改一下
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
]
```
