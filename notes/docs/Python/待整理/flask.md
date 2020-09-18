# Flask

> 从浏览器如何访问页面讲起，通过路由

## 基本配置

```python
# 导入Flask类
from flask import Flask

# 实例化
# 初始参数__name__，表示以当前模块所在路径作为根目录(/)
# 默认/static作为静态文件目录，/templates作为模版目录
app = Flask(__name__)


# 装饰器用于绑定路由，# 当用户访问该URL时将触发相应的视图函数
@app.route('/')
# 可同时绑定多个
@app.route('/hello')
# 定义视图函数
def hello():
    # 将信息或模版返回给前端显示
    return 'Hello NaiTangMao'


if __name__ == "__main__":
    #启动flask程序
    app.run()
```

## debug参数配置

### 方式1，run方法中配置debug参数

```python
app.run(debug=True)
```

### 方式2，给config配置类直接赋值

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

### 方式3，通过from_mapping()读取字典或元祖

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

### 方式4，通过from_object()读取模块

- 无需导入模块

```python
# 创建一个/config.py模块
DEBUG = True

# 传入字符串，会自动在根目录寻找叫做config的模块
app.config.from_object("config")
```

- 需要导入模块

```python
# 创建一个/config.py模块
DEBUG = True

import config
app.config.from_object(config)
```

- 区分环境

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

### 方式5，通过from_pyfile()读取配置文件

```python
# 创建一个/config.cfg文件，后缀名随意
DEBUG = True


app.config.from_pyfile('config.cfg')
```

### 方式6，通过from_json()读取json文件

```python
# 创建一个/config.json文件
{
  "DEBUG": true,
  "TESTING": false,
  "A": 123
}


app.config.from_json('config.json')
```

### 方式7，通过from_envvar()读取系统环境变量

```python
app.config.from_envvar("variable_name")
```

## 视图函数

两种注册方式

1. 装饰器@app.route("hello")
2. app.add_url_rule()函数（其实装饰器它调用的也是这个）：`app.add_url_rule("/hello", view_func=hello)`

### 唯一url

视图函数末尾加/，可以兼容url带/的情况（自动重定向），但不建议这样做（因为没有遵循唯一url，会索引两次，影响SEO）

### 视图函数的return和普通函数的return不一样

它是被封装过的一个Response对象，可以用postman请求[](http://127.0.0.1)查看返回的信息
content-type，默认为text/html，此外还有普通字符串（text/plain） 和 json（application/json）等
status code：200/404/301
http headers

可以通过引入make_response然后创建一个response对象
from falsk import make_response
response = make_response("返回内容", 状态码)
response.headers = headers
但是这样很麻烦，可以直接 return "返回内容", 状态码, headers

## 理解 if __name__== "__main__"

只有，main.py作为启动文件才会执行这里的代码，被导入时不会执行，把app.run()放在这里，在开发环境下，启动flask自带服务器时会执行到，但部署到生产环境，ngnix作为前置服务器接收浏览器发来的请求，然后转发给uwsgi，然后uwsgi把main.py当作模块加载，所以此时main.py不再是入口文件，即不会执行

## 开始

当我们要开始一个项目时候，最难的就是不知道如何要开始它

## api

基地址：http://t.yushu.im
关键字搜索：http://t.yushu.im/v2/book/search?q={}&start={}&count={}
isbn搜索：http://t.yushu.im/v2/book/isbn/{isbn}
豆瓣api(有访问频率限制，150次/h)：https://api.douban.com/v2/book

## 规范

入口模块中最好不要直接写一堆具体细节的逻辑判断等，而是抽象成函数在其他模块中引用进来，方便阅读
我们在阅读开源代码时也一样，不要执着于细节，第一遍读整体时候没必要去看函数具体的实现细节，知道干嘛的就可以了


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
