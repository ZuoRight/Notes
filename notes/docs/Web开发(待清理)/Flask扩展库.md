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
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy(app)
```

## Flask-Login

`pip install flask-login`

```python
from flask_login import LoginManager, UserMixin

login_manager = LoginManager(app)

class User(db.Model, UserMixin):
    pass
```
