# 模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <link rel="stylesheet" type="text/css" href="{% static 'xxx.css' %}"> 
</head>
<body>

</body>
</html>
```

## 防止跨站点请求伪造

所有针对内部 URL 的 POST 表单都应该使用 `{% csrf_token %}` 模板标签，防止跨站点请求伪造，确保表单是从实际的用户界面提交的，而不是从第三方网站。

```html
<form action="{% url 'one_app:vote' question.id %}" method="post">
    {% csrf_token %}
    <!-- 表单字段 -->
</form>
```

## 去除硬编码URL

```html
<a href="/one_app/{{ question.id }}/"> xxx </a>
<a href="{% url 'one_app:detail' question.id %}"> xxx </a>  <!-- 去除后 -->
```

## 过滤器

过滤器的`|`符号左右最好不要加空格，否则被for循环时会报错

自定义过滤器

![20210621191818](http://image.zuoright.com/20210621191818.png)

## 静态文件

```html
<!-- 文件存放在one_app/static中 -->
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'css/style.css' %}">

<img width="180" height="180" src="{% static 'img/xxx.jpg' %}"
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

## 表单

### 创建表单

`forms.py`

- 表单可以独立于模型使用，不需要数据迁移

```python
from django import forms

class MyForm(forms.Form):
    name = forms.CharField(label='Your Name', max_length=100)
    message = forms.CharField(widget=forms.Textarea)
```

- 也可以关联到已有模型，自动根据模型字段生成表单字段

```python
from django.forms import ModelForm
from .models import Contact

class ContactForm(ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'message']
```

### 实例化表单

`views.py`

```python
from django.shortcuts import render
from .forms import MyForm

def my_view(request):
    if request.method == 'POST':
        form = MyForm(request.POST)
        if form.is_valid():
            # 处理表单数据
            pass
    else:
        form = MyForm()

    return render(request, 'my_template.html', {'form': form})

```

### 渲染表单

`templates/my_template.html`

- 自动渲染

```html
<form method="post">
    {% csrf_token %}
    {{ form }}
    <button type="submit">Submit</button>
</form>
```

- 手动渲染

```html
<form method="post">
    {% csrf_token %}
    <div>
        {{ form.name.label_tag }} {{ form.name }}
    </div>
    <div>
        {{ form.message.label_tag }} {{ form.message }}
    </div>
    <button type="submit">Submit</button>
</form>
```
