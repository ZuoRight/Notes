# Flask扩展库

```python
from flask import Flask

app = Flask(__name__)
```

Flask有大量第三方扩展库，这些扩展可以简化和第三方库的集成工作，一般需要以下几步：

1. 安装
2. 导入
3. 实例化

## Flask-SQLAlchemy

`pip instal flask-sqlalchemy`

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import os
import sys

# 配置'SQLALCHEMY_DATABASE_URI'告诉 SQLAlchemy 数据库连接地址，不同数据库变量值格式不同
# 配置SQLite
WIN = sys.platform.startswith('win')
if WIN:  # 如果是 Windows 系统，使用三个斜线
    prefix = 'sqlite:///'
else:  # 否则使用四个斜线
    prefix = 'sqlite:////'

app.config['SQLALCHEMY_DATABASE_URI'] = prefix + os.path.join(app.root_path, 'data.db')  # SQLite
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False  # 关闭对模型修改的监控

# 加载完配置后再实例化
db = SQLAlchemy(app)
```

Flask-SQLAlchemy配置文档：<https://flask-sqlalchemy.palletsprojects.com/en/2.x/config/>

```python
# 创建数据库模型，继承自db.Model
class User(db.Model):  # 表名：user
    id = db.Column(db.Integer, primary_key=True)  # 主键
    name = db.Column(db.String(20))  # 名字
```

执行`flask shell`激活程序上下文，可以在命令行操作数据库

```python
from app import db, User
# 创建表
db.create_all()
# 删除表，会删除表中所有数据
# 如果不想删除可以使用数据迁移工具： Alembic（相应flask扩展：Flask-Migrate ）
db.drop_all()

user =User(name='7c')  # 创建一条User记录
db.session.add(user)  # 把记录添加到数据库会话
db.session.commit()  # 提交数据库会话

user = User.query.first()  # 获取 User 模型的第一个记录（返回模型类实例）
user.name  # 对返回的模型类实例调用属性即可获取记录的各字段数据

User.query.all()
User.query.count()
User.query.get(1)
User.query.filter_by(name='7c').first()
User.query.filter(User.name=='7c').first()
```

## Flask-Login

`pip install flask-login`

```python
from flask_login import LoginManager, UserMixin
from flask_login import login_user, login_required, logout_user, current_user

login_manager = LoginManager(app)
# 加了login_required装饰器的视图函数会自动重定向到登陆页面
# 但需要设置下登录的视图函数是哪个
login_manager.login_view = 'login'
# login_manager.login_message = "自定义错误提示"


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']

        if not username or not password:
            flash('Invalid input.')
            return redirect(url_for('login'))

        user = User.query.first()
        # 验证用户名和密码是否一致
        if username == user.username and user.validate_password(password):
            login_user(user)  # 登入用户
            flash('Login success.')
            return redirect(url_for('index'))  # 重定向到主页

        flash('Invalid username or password.')  # 如果验证失败，显示错误消息
        return redirect(url_for('login'))  # 重定向回登录页面

    return render_template('login.html')


# flask-login提供了current_user变量，如果用户已登录， current_user 会返回当前登录用户的数据库记录对象
# current_user等同于User.query.first()

# User模型类继承UserMixin，让User类拥有几个用于判断认证状态的属性和方法
# 比如：is_authenticated属性，current_user.is_authenticated返回True表示已登陆
class User(db.Model, UserMixin):
    pass


# 如果视图函数同时处理GET和POST请求，GET请求时不禁止，而只禁止POST请求
# 则不能使用login_required装饰器禁止登陆，可以在POST请求逻辑中用current_user.is_authenticated来判断
@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        if not current_user.is_authenticated:  # 如果当前用户未认证
            return redirect(url_for('index'))  # 重定向到主页
```

```python
{% if current_user.is_authenticated %}
# 模版内容保护
{% endif %}
```
