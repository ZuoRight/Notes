# 路由

> <https://docs.djangoproject.com/zh-hans/5.1/topics/http/urls/>

URLconf 模块是 urlpatterns 到视图函数的映射，可以是多对一的

urlpatterns 是 `path()` 或 `re_path()` 实例的序列，第一次请求时编译，然后缓存，后面会使用缓存

通过 `ROOT_URLCONF` 设置根路由路径，通常指向项目的 `urls.py`

- `mysite/settings.py`

```python
ROOT_URLCONF = 'mysite.urls'
```

- `mysite/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

# 引入 app 的路由，可以使用 inlcude() 来包含其它 urlpatterns list
urlpatterns = [
    path('admin/', admin.site.urls),
    path('one_app/', include('one_app.urls')),
    path('', include('jobs.urls')),
]
```

- `one_app/urls.py`

```python
from django.urls import path
from . import views
from django.contrib.auth.views import LogoutView

# 添加命名空间，避免多个应用路由名称相同混淆
app_name = "one_app"  # 在父urlpatterns中使用include('one_app.urls')

urlpatterns = [
    # 格式：path(route, view, kwargs, name)
    """
    route：必选，路由，开头不需要加反斜杠(/)，结尾需要加
    view：必选，调用视图函数，给视图函数传入 HttpRequest 对象
    kwargs：可选，字典的形式给视图函数传递任意个关键字
    name：可选，给路由起名字，在项目任意位置引用
    """
    path('', views.index, name='index'),
    path('list/', views.list, name='list'),
    # 调用基于类的通用视图，需要使用 .as_view() 方法将类转换为视图函数
    path('login/', views.Login.as_view(), name='login'),
    path('logout/', LogoutView.as_view(), name='logout'),

    path("blog/<int:year>/", views.year_archive, {"foo": "bar"}),
    # 调用 views.year_archive(request, year=2005, foo='bar') 
]
```

## 使用转换器

```python
# 可以用尖括号来转换类型，比如匹配 /articles/2005/03/
path("articles/<int:year>/<int:month>/", views.month_archive),
# 对应的视图函数是：views.month_archive(request, year, month)

# 转换器类型（5.1版本后不再支持自定义路径转换器）
"""
int 匹配0或任意整数
str 匹配除了反斜杠(/)之外的非空字符串
path 匹配完整路径，包含反斜杠(/)
slug 匹配短标签，可以由字母数字下划线(_)连字符(-)组成
uuid 由数字和字母组成，字母必须小写，而且必须包含连字符-
"""
```

## 命名路由，反解析

路由命名后，即可通过名字反向解析出 URL，相比硬编码 URL，更灵活

```python
from django.urls import path
from . import views

urlpatterns = [
    # ...
    path("articles/<int:year>/", views.year_archive, name="archive"),
    # ...
]
```

- 通过模版标签反解析

```html
<!-- /articles/<nnnn>/ -->
<a href="{% url 'archive' 2012 %}">2012 Archive</a>
<a href="{% url 'archive' yearvar %}">{{ yearvar }} Archive</a>
```

- 通过 reverse 函数反解析

```python
from django.http import HttpResponseRedirect
from django.urls import reverse

def redirect_to_year(request):
    return HttpResponseRedirect(reverse("archive", args=[2006,]))

    # 视图名传递给快捷函数 redirect，会自动被 reverse 反解析
    return redirect("archive", year="2006")
```

- resolve 反解析

```python
from urllib.parse import urlparse
from django.urls import resolve

view, args, kwargs = resolve(urlparse(next)[2])
"""
服务 URL 的视图函数
传递给视图函数的参数
传递给视图函数的关键字参数
"""
```

## 使用正则路由 re_path

```python
# 要用 re_path，而不再是 path
# 命名组语法：(?P<name>pattern)，比如 (?P<year>[0-9]{4})
# 也可以不命名：([0-9]{4})，但不推荐
re_path(r"^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$", views.month_archive)
```
