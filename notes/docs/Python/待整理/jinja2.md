# Jinja2

```text
{# 注释 #}

{{ 变量 }}

{% 语句 %}
```

## 取值

`{{ infos.name }}`

## 过滤器

```python
{{ infos|操作 }}
{{ infos|操作1|操作2 }}
```

## 字符串相关过滤器

caplitalize：把值的首字母变大写
upper：把值转为大写
title：把值中的每个单词首字母转为大写
trim：把值的首尾空格去掉
reverse：反转字符串


## 列表相关过滤器

first：取第一个元素
last：取最后一个元素
sum：列表求和
sort：列表排序

## 循环

```python
{% for i in _list %}

{% endfor %}
```

## 渲染

```python
@app.route('/index')
def index():
    return render_template('模板名', 变量名='值')
```