# MongoDB

官方文档：<https://www.mongodb.com/zh-cn/docs/manual/introduction/>

基于 JSON Document 的数据库，不需要先建模，可动态增加新字段

数据库 > 集合 > 文档

文档存储在集合中，集合类似数据表

![20240714145638](https://image.zuoright.com/20240714145638.png)

## 安装

分社区版和企业版，企业版在开发环境可以免费使用，但生产环境需要付费。

GUI 客户端：[Compass](https://www.mongodb.com/try/download/compass)

### MacOS

<https://www.mongodb.com/zh-cn/docs/manual/tutorial/install-mongodb-on-os-x/>

- 使用 homebrew 安装

```shell
brew tap mongodb/brew  # 添加下载源
brew update  # 更新 homebrew
brew install mongodb-community@7.0  # 安装
`
会自动配置好数据库和日志路径等
/opt/homebrew/etc/mongod.conf
/opt/homebrew/var/log/mongodb
/opt/homebrew/var/mongodb
`

brew services list
brew services start mongodb-community@7.0  # 启动
```

- 使用 Tarball 安装

不推荐，配置以及升级等都比较麻烦，下载 `.tgz` 包：<https://www.mongodb.com/try/download/community> 

```shell
# 解压缩
tar -zxvf mongodb-macos-arm64-7.0.tgz

# 复制到PATH路径
sudo cp /path/to/the/mongodb-directory/bin/* /usr/local/bin/
sudo ln -s /path/to/the/mongodb-directory/bin/* /usr/local/bin/

# 创建数据库目录并设置权限
sudo mkdir -p ~/data/db
sudo chown <user> ~/data/db

# 创建日志目录并设置权限
sudo mkdir -p ~/data/log/mongodb
sudo chown <user> ~/data/log/mongodb

# 运行
mongod --dbpath ~/data/db --logpath ~/data/log/mongodb/mongo.log --fork
```

## 使用

连接数据库，默认连接到一个空的 `test` 数据库

```shell
# MongoDB Shell 是一个 JavaScript 和 Node.js REPL 环境
mongosh mongodb://username:password@localhost:27017/mydatabase
mongosh
`
Current Mongosh Log ID:	6692a1788e8f34402bcc7df2
Connecting to:		mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.2.12
Using MongoDB:		7.0.12
Using Mongosh:		2.2.12

For mongosh info see: https://docs.mongodb.com/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2024-07-13T23:46:38.377+08:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
------

test>
`

mongosh "mongodb+srv://mycluster.abcd1.mongodb.net/myFirstDatabase" --apiVersion 1 --username <username>
```

```js
// 查看有哪些数据库
show dbs
`
admin   40.00 KiB
config  12.00 KiB
local   40.00 KiB
`

db  // 显示当前数据库
use test  // 选择要使用的数据库，可以不存在

// 查看有哪些集合
show collections

db.createCollection(name, options)  // 创建集合，可以不存在
```

- 增

```js
// 向集合中插入文档
// 如果数据库或集合都不存在，则会在存储时自动创建
db.users.insertOne(  // 插入多条使用 insertMany
    {
        name: "sue",
        age: 26,
        status: "pending"
    }
)
```

- 查

```js
db.users.find(
    {age: {$gt: 18}},  // 查询条件
    {name: 1, address: 1}  // projection
).limit(5)  // cursor modifier
```

- 改

```js
db.users.updateMany(
    {age: {$lt: 18}},  // update filter
    {$set: {status: "reject"}}  // update action
)
```

- 删

```js
db.users.deleteMany(
    {status: "reject"}  // delete filter
)
```

## Pymongo

`pip install pymongo`

```python
import pymongo

client = pymongo.MongoClient("mongo://localhost:27017/")  # 连接服务器
db = client["test"]  # 选择数据库
collection = db["users"]  # 获取集合
insert_result = collection.insert_one({"name": "John", "address": "Highway 37"})  # 插入文档

# 查询集合中的所有文档
for doc in collection.find():
    print(doc)

# 更新文档
query = { "name": "John" }
new_values = {"$set": { "address": "Ocean View 2"}}
update_result = collection.update_one(query, new_values)

# 删除文档
delete_query = { "address": "Highway 37" }
delete_result = collection.delete_one(delete_query)
```
