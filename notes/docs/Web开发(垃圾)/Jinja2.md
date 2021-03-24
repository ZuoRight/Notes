# Jinja2

app.py

```python
from flask import Flask, render_template

app=Flask(__name__)


@app.route('/')
def index():
    key = value
    # 模版渲染
    return render_template('index.html', info=key)
```

index.html

```text
{# 注释 #}
{{ 变量 }}
{% 语句 %}


{{ info }}
{{ info[key] }}


{% if info == v1 %}
    判断语句
{% elif info = v2 %}
    ...
{% else %}
    ...
{% endif %}


{% for i in info %}
    循环语句
{% endfor %}
```

## 变量过滤器

`{{ info|操作1|操作2 }}`

- 字符串相关过滤器

```html
caplitalize：把值的首字母变大写
upper：把值转为大写
title：把值中的每个单词首字母转为大写
trim：把值的首尾空格去掉
reverse：反转字符串
```

- 列表相关过滤器

```html
first：取第一个元素
last：取最后一个元素
sum：列表求和
sort：列表排序
```
