# 视图

## HttpRequest

`views.py`

```python
from .models import Question  # 引入模型
from django.shortcuts import render  # 引入快捷函数


def index(request):  # request=<HttpRequest object>
    # 通过request.POST字典对象获取表单提交的数据
    # 取不到值会报KeyError，取到的值永远是字符串
    try:
        xxx = request.POST["key_name"]
    except (KeyError, Choice.DoesNotExist):
        pass
    else:
        pass

    return render(request, 'one_app/index.html', context)
```

## HttpResponse

- 方式1，HttpResponse返回文本

```python
from django.http import HttpResponse

def index(request):
    ...
    output = xxx
    return HttpResponse(output)
```

- 方式2，HttpResponse返回模板

```python
from django.http import HttpResponse
from django.template import loader

def index(request):
    a = xxx
    context = {"key": a}  # 上下文

    template = loader.get_template('one_app/index.html')  # 载入模板文件
    return HttpResponse(template.render(context, request))  # 传递上下文
```

- 方式3，HttpResponse用快捷函数形式返回模板（推荐）

```python
from django.shortcuts import render, get_object_or_404

def index(request):
    a = xxx
    context = {"key": a}

    # 格式：render(request对象，模板名称，上下文字典)
    return render(request, 'one_app/index.html', context)
```

## 重定向

```python
from django.http import HttpResponseRedirect

# 构造函数：HttpResponseRedirect(重定向后的URL)
# 视图函数中避免URL硬编码：reverse(视图名字，args=(视图所需参数的元组,))
return HttpResponseRedirect(reverse('one_app:results', args=(question.id,)))  # /one_app/3/results/
```

## 404

- 方式1

```python
from django.http import Http404
from django.shortcuts import render

from .models import Question

def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'one_app/detail.html', {'question': question})
```

- 方式2，快捷函数方式返回（推荐）

```python
from django.shortcuts import get_object_or_404, render

from .models import Question

def detail(request, question_id):
    # 格式：get_object_or_404(模型对象，任意数量的关键字参数)
    # 如果关键字参数查不到结果则返回404
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'one_app/detail.html', {'question': question})
```

## 路由配置 urls.py

- 配置项目的路由，根路由匹配应用

```python
# mysite/urls.py
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    # 必选参数view
    path('one_app/', include('one_app.urls')),  # 如果要引用其它的URLconfs，应该总是使用inlcude()这种形式
    path('admin/', admin.site.urls),  # admin.site.urls是唯一的例外
]
```

- 配置应用的路由，应用匹配页面

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
    path('', views.index, name='index'),
]
```

## 通用视图

普通视图使用函数来定义，更灵活，通用视图使用类来定义，适用于许多常见的 CRUD 操作和标准用例，实际项目中，通常可以同时使用这两种类型的视图，以满足不同的需求

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

## 模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <link rel="stylesheet" type="text/css" href="{百分号 static 'xxx.css' 百分号}"> 
</head>
<body>

</body>
</html>
```

### 防止跨站点请求伪造

所有针对内部 URL 的 POST 表单都应该使用 {百分号 csrf_token 百分号} 模板标签，防止跨站点请求伪造。

```html
<form action="{百分号 url 'one_app:vote' question.id 百分号}" method="post">
{百分号 csrf_token 百分号}
</form>
```

### 去除硬编码URL

```html
<a href="/one_app/{{ question.id }}/">xxx</a>
<!-- 去除后 -->
<a href="{百分号 url 'one_app:detail' question.id 百分号}">xxx</a>
```

### 过滤器

过滤器的`|`符号左右最好不要加空格，否则被for循环时会报错

自定义过滤器

![20210621191818](http://image.zuoright.com/20210621191818.png)

### 静态文件

```html
<!-- 文件存放在one_app/static中 -->
{百分号 load static 百分号}
<link rel="stylesheet" type="text/css" href="{百分号 static 'css/style.css' 百分号}">

<img width="180" height="180" src="{百分号 static 'img/xxx.jpg' 百分号}"
```

DEBUG 设置为 False 后 Django 不会再提供静态文件服务

- 方式1(适用于命令行启动)：`python manage.py runserver --insecure`
- 方式2 CDN

```text
1. 配置文件中先把所有注册APP分散的静态资源都收集到统一位置：STATIC_ROOT = os.path.join(BASE_DIR, 'static_cdn')
2. 命令行执行收集命令：python manage.py collectstatic，将项目用到的所有静态文件集中存放在 static_cdn 路径下
3. 然后用Nginx提供静态资源服务
    location /static/ {
        alias /usr/share/nginx/mysite/static_cdn/;
    }
4. 重新加载：service nginx reload
```

## 数据库API

> <https://docs.djangoproject.com/zh-hans/4.0/ref/models/querysets/#queryset-api-reference>

```python
# 可用于admin.py或views.py
from one_app.models import Blog

# SELECT语句
Q = Blog.objects  #  Managerd对象，表级操作
Q.all()  # 等同于Q，返回一个包含所有记录的QuerySet对象集合
Q.order_by("xxx")  # 按某字段排序

# WHERE子句
r = Q.get(pk=1)  # 如果查询结果只有一条记录可以用get，但如果查不到或查到多条会报错
r.xxx  # 会重新访问数据库
r = Q.select_related("xxx").get(pk=1)
r.xxx  # 不需要重新访问数据库，性能更高

Q.filter(**kwargs)  # 过滤满足条件的记录，WHERE xxx AND xxx
Q.exclude(**kwargs)  # 排除满足条件的记录，WHERE NOT(xxx AND xxx)
Q.exclude().exclude()  # WHERE NOT xxx AND NOT xxx

# LIMIT和OFFSET子句，切片，不支持负数索引，可以用步长2
Q.all()[:5]  # LIMIT 5 返回前5个对象
Q.all()[5:10]  # OFFSET 5 LIMIT 5 返回第6～10个对象

r = Blog(Field1="x1", Field2="x2")  # 实例初始化，行级操作
r.Field1 = "x01"  # 修改字段值

r.save()  # 保存
```

- 字段查询

```python
# 完全匹配 =
Q.get(name__exact="abc")  # WHERE name = "abc";
Q.get(name="abc")  # 同上

# 比较
__gt  # 大于
__gte  # 大于等于
__lt   # 小于
__lte  # 小于等于

# LIKE 区分大小写
Q.get(name__contains="abc")  # WHERE name LIKE '%abc%';
Q.filter(name__startswith="abc")  # WHERE name LIKE 'abc%';
Q.filter(name__endswith="abc")  # WHERE name LIKE '%abc';

# ILIKE 不区分大小写
Q.get(name__iexact="abc")  # WHERE name ILIKE 'abc';
Q.get(name__icontains="xxx")  # WHERE name ILIKE '%abc%';
Q.get(name__istartswith="abc")  # WHERE name ILIKE 'abc%';
Q.get(name__iendswith="abc")  # WHERE name ILIKE '%abc';

# IN
Q.filter(id__in=[1, 3, 4])  # WHERE id IN (1, 3, 4);

# BETWEEN...AND...
Q.filter(date__range=(start_date, end_date))  # WHERE date BETWEEN 'start_date' and 'end_date';
```
