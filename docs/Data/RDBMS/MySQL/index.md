# 引言

[官方手册](https://dev.mysql.com/doc)

MySQL 在设计上受 Oracle 影响颇深

MySQL 是典型的 C/S 架构

![20220809201603](https://image.zuoright.com/20220809201603.png)

## 客户端

可以通过命令行或者 GUI 管理工具来与服务端交互

### 命令行

MySQL Shell 除了支持 SQL 语句，还支持 JavaScript 和 Python 脚本，并且支持调用 MySQL API 接口

```shell
# 连接服务
mysql -h$ip -P$port -u$user -p  # -uroot 这样连着写也没有问题
mysql -h 127.0.0.1 -u root -p  # 12345678
# -h 默认主机名为本机 127.0.0.1
#   不可以与localhost混用
#       当设置为 127.0.0.1 时，系统通过 TCP/IP 方式连接数据库
#       当设置为 localhost 时，系统通过 socket 方式连接数据库
# -P 默认端口号 3306
# -u 指定用户名，默认为本机 user 名
# -p 带此参数表明需要密码，反之不需要
#   回车后密文输入
#   也可以将密码直接写在 -p 命令后，但不建议这样，容易泄漏

SELECT VERSION();  # 查看版本
SELECT NOW()  # 查看当前时间

show databases;  # exit
create database;
use demo;
show tables;

# 断开连接
exit  # 或者quit
```

### GUI 管理工具

- 官方：[Workbench](https://dev.mysql.com/downloads/workbench/)（体验较差）
- 免费：Navicat Premium Lite（推荐）、DBeaver、SQLyog 等（小巧、够用）
- 付费：Navicat、DataGrip
- 在线：phpMyAdmin 需要自己搭建

## 服务端

- 启动/停止服务

```shell
# Mac
sudo mysql.server start/stop
# 也可以通过系统设置面板控制
# 如果是 homebrew 安装的，也可以通过 brew services 控制

# Windows
net start/stop mysql
```

### 连接层

负责与客户端交互，管理连接，权限验证

建立连接时，用户名密码认证通过，连接器会到权限表里面查出用户所拥有的权限，然后将用于此次连接中的所有与权限相关的逻辑处理，也就是说如果管理员对用户修改了权限，需要重新连接才会生效。

![20241014214124](https://image.zuoright.com/20241014214124.png)

连接后没有动作，将处于空闲状态（Sleep），如果太长时间没动作，连接器就会自动断开（由 wait_timeout 控制，默认 8h）

- 短连接，是指每次执行完很少的几次查询就断开连接，下次查询再重新建立一个
- 长连接，是指连接成功后，如果客户端持续有请求，则一直使用同一个连接

建立连接的过程比较复杂，所以通常建议使用长连接，但由于内存资源在断开连接时才会释放，所以长连接会使内存占用不断增加，导致过大而被系统强行杀掉重启

优化方式

- 使用一段时间，定期断开长连接
- 执行一个比较大的操作后，就断开连接，之后要查询再重连
- 执行一个比较大的操作后，通过执行 mysql_reset_connection 来重新初始化连接资源（不需要重连和重新做权限验证，将连接恢复到刚刚创建完时的状态）

### SQL 层

对 SQL 语句进行查询处理，涵盖 MySQL 的大多数核心服务功能

![20220809202624](https://image.zuoright.com/20220809202624.png)

- 查询缓存：命中则直接返回结果

MySQL 8.0 以下的版本，如果设置了查询缓存，执行器会将查询结果以 key-value 对的形式进行缓存，key 是查询的语句，value 是查询的结果。

MySQL 8.0 及以上版本，已经删除了缓存功能，因为当一个表被更新，所有缓存都会被清空，以至于缓存利用率过低。

- 解析器（要做什么）：对 SQL 语句进行词法和语义分析
- 优化器（要怎么做）：生成执行计划，确定 SQL 语句的执行路径
    - 比如，表里面有多个索引的时候，决定使用哪个索引
    - 比如，语句有多表关联的时候，决定各个表的连接顺序
- 执行器（开始执行）：在执行之前需要判断该用户是否具备权限，如果具备权限就执行并返回结果

### 存储引擎

存储数据，提供读写接口

不同存储引擎的表数据存取方式不同，支持的功能也不同

MySQL 的存储引擎采用了插件形式，允许开发人员设置自己的存储引擎。

> 注意：同一数据库的不同表也可以采用不同的存储引擎

- Memory：使用系统内存作为存储介质，以便得到更快的响应速度。但如果 mysqld 进程崩溃，则会导致所有的数据丢失，因此我们只有当数据是临时的情况下才使用 Memory 存储引擎。
- MyISAM：MySQL 5.5 版本之前默认的存储引擎，是 MySQL 自带的引擎，最大的特点是速度快，占用资源少，但不支持事务，也不支持外键。
- InnoDB：MySQL 5.5 版本之后默认的存储引擎，是 Innobase Oy 公司开发，以插件形式引入 MySQL 的，最大的特点是支持事务、行级锁定、外键约束等。
- NDB Cluster，主要用于 MySQL Cluster 分布式集群环境，类似于 Oracle 的 RAC 集群。
- Archive：它有很好的压缩机制，用于文件归档，在请求写入时会进行压缩，所以也经常用来做仓库。

## WAL 机制

WAL(Write-Ahead Logging 预写日志) 是一种为数据库系统提供原子性和持久性的技术族（ACID 中的 A 和 D），要求所有更新操作在写入磁盘前，都需要先预写到效率更高的日志中。

目的是，当程序在执行某操作时遇到崩溃或者断电等异常重启后，可以通过检查日志来恢复正常（crash-safe）。

就像记账时，先写到里临时的小黑板上，空闲时再整理到总账上。

MySQL 使用 redo log（重做日志）和 binlog（归档日志）实现 WAL 机制

> MySQL 自带的引擎 MyISAM 没有 crash-safe 能力，所以不支持事务，后来通过插件形式引入 InnoDB 引擎，拥有了 redo log 才有了 crash-safe 能力并得以支持事务。

![20241015225716](https://image.zuoright.com/20241015225716.png)

> 浅绿色表示在 InnoDB 中执行  
> 深绿色表示在执行器中执行

binlog 是逻辑日志，处于执行器中，记录的是这个语句的原始逻辑，可以追加写入

```text
Timestamp: 1633456789
Event Type: QUERY
Server ID: 1
End_log_pos: 256
SQL Statement: INSERT INTO employees (name, position) VALUES ('John Doe', 'Engineer');
```

redo log 是物理日志，记录的是在某个数据页上做了什么修改，空间固定会用完，所以是循环覆盖写入

```text
LSN: 1001
Type: INSERT
Space ID: 5
Page Number: 3
Offset: 128
Data Length: 30
Data: [Binary data representing the row change]
```

![20241015230446](https://image.zuoright.com/20241015230446.png)

> write pos 是当前记录的位置，一边写一边后移  
> checkpoint 是当前要擦除的位置

## 下载安装

[官方下载链接](https://dev.mysql.com/downloads/mysql/)

### Mac

选择 LTS 版本，M1+ 选择 ARM OS 版本，点击 `No thanks, just start my download` 下载，安装时，一直下一步即可

![20241124195759](https://image.zuoright.com/20241124195759.png)

Config Type 服务器类别，区别在于数据库服务占用内存不同

- Development Computer 开发计算机，占用所需最小的内存，以便其他应用可以正常运行
- Sever Computer 服务器计算机，会假设在这台计算机上有多个数据库服务器实例在运行，占用中等程度的内存
- Dedicated Computer 专属计算机，占用计算机的全部内存资源

连接方式

- 命名管道（Named Pipe）速度快，但只能从本机访问数据库服务器
- 共享内存（Shared Memory）同上
- 网络通讯协议（TCP/IP）速度慢，但可以通过网络进行访问，默认端口 3306

最后会默认创建 `root` 账户，需要设置一个密码（最少 8 位）

加密方式

- `mysql_native_password` 使用明文（Use Legacy Password Encryption）
- `caching_sha2_password`，8.0 版本新增，更安全，某些第三方客户端连接数据库时不兼容新密码验证方式可能会报错

然后配置环境变量

```shell
export PATH=$PATH:/usr/local/mysql/bin
export PATH=$PATH:/usr/local/mysql/support-files
```

### Windows

下载后是个压缩包，无需安装，解压后做一些相关配置即可使用

首先在解压后文件的目录下，新建一个配置文件：`my.ini`，然后设置以下内容

```ini
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录，注意，这里要用两个反斜线\\
basedir=C:\\path\\mysql-8.0.27-winx64
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# datadir=C:\\path\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

然后设置环境变量

- `MYSQL_HOME`:`安装路径`
- `Path`:`%MYSQL_HOME%\bin`

然后以管理员身份打开命令行，输入：`mysqld --initialize --console`，初始化完毕后会生成一个初始密码（记得保存）

然后输入：`mysqld install`，注册服务，成功后即可启动服务登录使用

### Docker

镜像

- Docker 官方提供的（推荐）：<https://hub.docker.com/_/mysql>
- Oracle 官方提供的 mysql-server（只适用于 Linux）：<https://hub.docker.com/r/mysql/mysql-server>

```shell
docker pull mysql:8.0
sudo docker run --name mysql8
    -e MYSQL_ROOT_PASSWORD=passwd \  # 设置root用户的密码，此参数必选
    --network host \  # 绑定网络，方便其它容器使用mysql服务，这里绑定到host网络
    -d mysql:8.0

# 默认创建'root'@'localhost'帐户，设置密码，三种方式必选其一：
    # 1. 固定密码（出于安全考虑不建议这样）：-e MYSQL_ROOT_PASSWORD=passwd
    # 2. 密码为空：-e MYSQL_ALLOW_EMPTY_PASSWORD=yes
    # 3. 随机生成密码（推荐）：-e MYSQL_RANDOM_ROOT_PASSWORD=yes
        '''
        从容器日志中查找密码：sudo docker logs mysql8 2>&1 | grep GENERATED
        连接服务：sudo docker exec -it mysql8 mysql -u root -p
        重置密码：ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';

        默认创建的root用户貌似只能在容器内使用，无法被外网或其它容器访问（即使设置了host=%）
        可以新创建一个用户
            USE mysql;
            CREATE USER 'demo'@'%' IDENTIFIED BY 'password';
            flush privileges;
        '''
# 数据存放路径：/var/lib/mysql
#   默认挂载在宿主机的：/var/lib/docker/volumes/<container_id>，需要root权限才能访问
#   可以挂载到自定义的路径（不能使用被其它容器已占用的路径）：-v /my/own/datadir:/var/lib/mysql
# 配置文件路径：/etc/mysql/my.cnf，如果想用自定义的配置文件启动可以-v挂载

# 映射到宿主机的23306端口，避免与宿主机数据库端口冲突：-p 23306:3306
# 使用host网络模式则无法也无需指定

# 连接到容器
docker exec -it mysql8 mysql -uroot -p
mysql -h 127.0.0.1 -u root -p  # 使用 -h localhost 可能无法连接
```
