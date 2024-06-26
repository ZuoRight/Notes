# 基本使用

官方文档：<https://flask.palletsprojects.com/en/2.2.x/>

```python
# app.py

from flask import Flask

# 实例化
app = Flask(__name__)  # __name__，表示以当前模块所在路径作为根目录：/
"""
/static 作为静态文件目录
/templates 作为模版目录
"""

# 装饰器用于绑定路由，当用户访问该URL时将触发相应的视图函数
# 可同时绑定多个
@app.route('/')
@app.route('/hello')
# 定义视图函数
def hello():
    # 将信息或模版返回给前端显示
    return 'Hello, World!'
```

## 基本命令

### 运行项目

- 代码中运行

```shell
if __name__ == '__main__':
    app.run(debug=True)
```

- 命令行运行

```shell
flask --app demo run
```

### 命令

进入 Shell：`flask shell`


```python
import click

@app.cli.command()  # 注册为命令
@click.option('--drop', is_flag=True, help='Create after drop.')  # 设置选项
def xxx(drop):  # xxx即是命令名称
    """Initialize the database."""
    if drop:  # 判断是否输入了选项
        db.drop_all()
    db.create_all()
    click.echo('Initialized database.')  # 输出提示信息
```

## 视图函数

### 路由注册方式

1. `app.add_url_rule(/hello)`
2. `@app.route("/hello")`，装饰器方式调用的也是方式一的函数

### 唯一路由

视图函数末尾不建议加/，它会自动重定向到不带/的路由，没有遵循唯一路由原则，相当于索引两次，不利于Seo

### 路由转换器

```python
# 还可以设置类型<int:id>
@app.route('/login/<id>')
def login(id):
    pass
```

```python
from flask import escape

@app.route("/user/<name>")
def index(name):
    # 为防止输入数据包含恶意代码，所以不建议直接作为响应返回，反之代码注入
    # 可使用escape()函数进行转义处理，比如<转为&lt;
    return f"{escape(name)}"
```

### 获取请求数据

```python
from flask import request

# 请求路径
request.path

# 请求方式
request.method

# 表单数据，request.form是一个特殊的字典
request.form['name']
request.form.get('name')
request.form.getlist('name')

# 查询字符串
request.args.get("name")

# 获取raw格式
request.data

# 获取上传文件
request.files['the_file']
```

### 设置响应信息

- 通过元组直接返回（推荐）

```python
return (response, status, headers)
```

- 通过`make_response()`构造

```python
from falsk import make_response

res = make_response(response)
res.status = 404
res.headers[""key] = "value"

return res
```

- 返回错误

```python
from flask import abort, Response

# 方式1，返回状态码
abort(404)

# 方式2，返回响应体
res = Response('error')
abort(res)
```

## cookies

- 获取cookies

```python
from flask import request

request.cookies.get('key')
```

- 设置cookies

```python
from falsk import make_response

res = make_response(response)

# max_age设置有效时间，默认关闭浏览器就失效
res.set_cookie('key', 'value', max_age=3600)

# 删除cookiesn
res.delete_cookie("key")
```

## session

> 如果将数据保存至Cookies的话，任何人操作浏览器都可以更改Cookies，这样就会导致数据安全性很低。
>  
> 反之Flask中的session机制本质也是将数据保存在Cookies中，但是他对这些Cookies进行密钥签名，这就意味着用户可以查看你的Cookies内容，但却不能更改它，唯一能更改的前提就是你知道签名的密钥。

```python
from flask import session

# 配置签名密钥
"""
import os
# 随机生成密钥
print(os.urandom(24))
"""
app.config["SECRET_KEY"] = 'xxx'  # 方式1
app.secret_key = b'xxx'  # 方式2

# 设置session
session['key'] = 'value'

# 获取session
session.get('key')
```

## 上下文对象

用g修饰的变量可以在视图函数之间中相互调用

```python
@app.route('/set')
def set():
    g.username = 'x'

@app.route('/get')
def get():
    name = g.username
    pass
```

## 请求钩子

需要通过装饰器实现

```python
# 在处理第一个请求前执行
@app.before_first_request

# 在每次请求前执行
@app.before_request

# 在每次请求后执行，前提是没有未处理的异常抛出
@app.after_request(response)

# 在每次请求后执行，即使有未处理的异常，但debug模式需要为False
@app.teardown_request(response)
```

## 参数校验

```python
from wtforms import Form, IntegerField, StringField
from wtforms.validators import NumberRange, Length, URL, DataRequired

class ToolsForm(Form):
    # message="错误提示"
    word_number = IntegerField(validators=[NumberRange(min=10, max=10000)], default=10)
    activity_url = StringField(validators=[URL(require_tld=True)])
```

```python
form = ToolsForm(request.args)
# 如果参数校验成功执行
if form.validate():
    # 这里从Form取值而不是从reqFuest.args取值，是因为那样取不到默认值
    word_number = form.word_number.date
# 参数校验失败则执行
else:
    return xxx
```

## url_for()

```python
from flask import Flask, url_for

@app.route('/hello/<name>')
def index(name):
    return f'Hello, {name}'

url_for("index", name=xxx)  # 生成url: /hello/xxx
url_for("static", filename="xxx.jpg")  # 生成url: /static/xxx.jpg
```

## 重定向

```python
from flask import Flask, url_for, redirect


@app.route("/1")
def func1():
    # return redirect( "/2")  # 方式1
    return redirect(url_for('func2'))  # 方式2

@app.route('/2')
def func2():
    pass
```

## 404错误处理函数

```python
@app.errorhandler(404)  # 传入要处理的错误代码
def page_not_found(e):  # 接受异常对象作为参数
    return render_template('404.html'), 404  # 返回模板和状态码
```

## 模板上下文处理函数

对于多个模板内都需要使用的变量，我们可以使用 app.context_processor 装饰器注册一个模板上下文处理函数

```python
@app.context_processor
def inject_user():  # 函数名可以随意修改
    user = User.query.first()
    return dict(user=user)  # 需要返回字典，等同于 return {'user': user}
```

## 读取配置

配置文档：<https://flask.palletsprojects.com/en/1.0.x/config/>

配置变量名称必须大写，写入配置的语句一般会放到扩展类实例化语句之前

Flask 提供了一个统一的接口来写入和获取这些配置变量：Flask.config 字典

```python
app.config["KEY"] = value
```

在Flask项目开发中，可以将隐私数据写到`.env`中，而一些不隐私的可以写到`.flaskenv`文件中

## debug 参数配置

- 方式1，run方法中配置debug参数

```python
# 在开发环境下，flask自带服务器启动时，作为入口文件会执行
if __name__ == "__main__":
    app.run(debug=True)
# 在生产环境，ngnix作为前置服务器接收浏览器发来的请求，然后转发给uwsgi，然后uwsgi把main.py当作模块加载，所以此时main.py不再是入口文件，即不会执行
```

- 方式2，给config配置类直接赋值

```python
# 注意，配置名需要全大写
# 会覆盖掉默认值：{"DEBUG":False}
app.config["DEBUG"] = True

# 同时设置多个值
app.config.update(
    "DEBUG" = True,
    "TESTING" = True
)

# 如何取值呢
app.config.get("DEBUG")
```

- 方式3，通过from_mapping()读取字典或元祖

```python
# 创建一个字典对象
config = {
    'DEBUG': True,
    'TESTING': False
}

# 或者一个二维元组对象
config = (('DEBUG', True), ('TESTING', False))


app.config.from_pyfile(config)
```

- 方式4，通过 from_object() 读取模块

```python
# 创建一个/config.py模块
DEBUG = True

# 传入字符串，会自动在根目录寻找叫做 config 的模块
app.config.from_object("config")

# 或者
import config
app.config.from_object(config)
```
区分环境

```python
# 创建一个/config.py模块
# 各个环境均继承默认配置类，根据环境覆盖不同属性
class Config(object):
    DEBUG = False
    TESTING = False
    DATABASE_URI = "xxx"

# 生产环境
class ProductionConfig(Config):
    DATABASE_URI = 'yyy'

# 预发环境
class TestingConfig(Config):
    TESTING = True

# 开发环境
class DevelopmentConfig(Config):
    DEBUG = True


import config
app.config.from_object(config.DevelopmentConfig)
```

- 方式5，通过from_pyfile()读取配置文件

```python
# 创建一个/config.cfg文件，后缀名随意
DEBUG = True


app.config.from_pyfile('config.cfg')
```

- 方式6，通过from_json()读取json文件

```python
# 创建一个/config.json文件
{
  "DEBUG": true,
  "TESTING": false,
  "A": 123
}


app.config.from_json('config.json')
```

- 方式7，通过from_envvar()读取系统环境变量

```python
app.config.from_envvar("variable_name")
```
