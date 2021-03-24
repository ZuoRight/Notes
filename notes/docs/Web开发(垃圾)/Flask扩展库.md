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

login_manager = LoginManager(app)

class User(db.Model, UserMixin):
    pass
```
