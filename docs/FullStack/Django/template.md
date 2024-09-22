# 表单与模版

概览：<https://docs.djangoproject.com/zh-hans/5.1/topics/forms/>

API：<https://docs.djangoproject.com/zh-hans/5.1/ref/forms/api/>

在 HTML 中，表单是在 `<form>...</form>` 中的一些元素，它允许访客做一些类似输入文本、选择选项、操作对象或空间等动作，然后发送这些信息到服务端。

处理表单时会用到 GET 和 POST 两种方法，分别用于不同的目的。

GET 提交的数据被捆绑到一个字符串中，拼接到 URL 中，比如 `?q=forms&release=1`，用 GET 管理表单会有安全风险，很容易被保存、分享、重新提交，所以通常用于不会影响系统状态的请求，比如搜索表单。

POST 提交的表单数据会被浏览器封装，编码后传输给服务端，并接收响应，用 POST 管理表单，加上 CSRF 等保护措施，可以对访问进行更多控制，通常用于更改数据库的请求。

处理表单很繁琐，但 Django 会帮助我们完成大部分工作

- 准备并重组数据，为数据创建表单
- 在视图中获取并实例化表单，将它传递给模板上下文
- 使用模板变量将它渲染成 HTML 标签
- 接收并处理客户端提交的表单及数据

表单的核心组件 Form 类，用于描述一张表单并决定它如何工作及呈现

比如有如下表单

```html
<form action="/your-name/" method="post">
    <label for="your_name">Your name: </label>
    <input id="your_name" type="text" name="your_name" value="{{ current_name }}">
    <input type="submit" value="OK">
</form>
```

## 构建表单 `forms.py`

- 独立于模型，手动构建

```python
from django import forms

# 类似构建模型，但不涉及数据迁移
class MyForm(forms.Form):
    name = forms.CharField(label='Your Name', max_length=100)
    # widget 参数让字段可以使用非默认控件
    message = forms.CharField(widget=forms.Textarea)
```

表单字段：<https://docs.djangoproject.com/zh-hans/5.1/ref/forms/fields/>

widget：<https://docs.djangoproject.com/zh-hans/5.1/ref/forms/widgets/>

每个字段都有一个相对应的控件类，并对应 HTML 表单控件，用于管理表单数据并在提交表单时执行验证

比如 CharField 对应 TextInput 控件类，它会在 HTML 中生成一个 `<input type="text">`

- 从已有模型，自动构建

<https://docs.djangoproject.com/zh-hans/5.1/topics/forms/modelforms/>

ModelForm 通过 Form 映射 Model 的字段到 HTML 表单的 `<input>` 元素，Django  Admin 就基于此。

```python
from django.forms import ModelForm  # 可理解为 Form 的子类
from .models import Contact

# 根据顺序，为 fields 中每个模型字段自动设置一个对应的表单字段
class ContactForm(ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'message']  # 包含的字段
        # exclude = ["title"] 需要排除的字段
        # localized_fields = ['birth_date'] 本地化字段
        # fielss = "__all__" 可以使用所有字段（默认本地化），有安全风险
```

模型属性与表单属性对应关系

Model|Form
--|--
blank=True|required=False
verbose_name|label=verbose_name(首字母大写)
help_text|help_text
choices|widget=Select

## 实例化表单 `views.py`

```python
from django.shortcuts import render
from .forms import MyForm

def my_view(request):
    if request.method == 'POST':
        # 从请求中获取相应的键/值对，绑定数据到表单，然后对其进行处理
        form = MyForm(request.POST)
        # 可以用 instance 为字段设置初始值
        """
        author = Author(title="Mr")
        form = PartialAuthorForm(request.POST, instance=author)
        """
        if form.is_valid():
            # 验证通过的表单数据会被传到 form.cleaned_data 字典中
            message = form.cleaned_data["message"]
            # form.save() 可以保存数据
            return HttpResponseRedirect("/thanks/")
        # 如果验证没通过，可以继续编辑表单
    else:
        # GET 请求，则创建并渲染一个空的表单实例
        form = MyForm()

    return render(request, 'demo.html', {'form': form})
```

有些字段类型需要一些额外的处理，比如使用表单上传文件，需要从 `request.FILES` 获取

## 渲染表单 `templates/demo.html`

所有的表单字段及其属性都将通过 Django 模板语言从 `{{ form }}` 中被解包成 HTML 标签

比如表单中的 name 字段将解包如下

> 可以通过 FORM_RENDERER 自定义用于解包的模版

```html
<!-- label 对应的值会显示到 <label> -->
<label for="your_name">Your name: </label>
<!-- 系统会根据 max_length 自动验证长度 -->
<input id="your_name" type="text" name="your_name" maxlength="100" required>
```

我们只需要提供 `<form>` 标签和提交按钮

action `/your-name/` 对应视图函数名称

```html
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit">
</form>
```

- 手动渲染

```html
<form action="/your-name/" method="post">
    {% csrf_token %}
    <div>
        {{ form.name.label_tag }} {{ form.name }}
    </div>
    <div>
        {{ form.message.label_tag }} {{ form.message }}
        {{ form.message.errors }}
    </div>
    <input type="submit" value="Submit">
</form>
```

## 模版

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <link rel="stylesheet" type="text/css" href="{% static 'xxx.css' %}"> 
</head>
<body>
    <p>Welcome!</p>

    <!-- 生成2段随机文本，默认b表示纯文本，p表示段落，w表示单词 -->
    {% lorem 2 p %}
</body>
</html>
```

## 静态文件

```html
<!-- 文件存放在 one_app/static 路径 -->
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

## 防止跨站点请求伪造

<https://docs.djangoproject.com/zh-hans/5.1/ref/csrf/>

Django 自带了一个简单易用的跨站点请求伪造保护，所有针对内部 URL 的 POST 表单都应该使用 `{% csrf_token %}`，确保表单是从实际的用户界面提交的，而不是从第三方网站。

```html
<form action="{% url 'one_app:vote' question.id %}" method="post">
    {% csrf_token %}
    <!-- 表单字段 -->
</form>
```

## 过滤器

过滤器的`|`符号左右最好不要加空格，否则被for循环时会报错

自定义过滤器

![20210621191818](http://image.zuoright.com/20210621191818.png)
