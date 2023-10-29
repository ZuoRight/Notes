# 引言

[官方手册](https://dev.mysql.com/doc)

[官方下载链接](https://dev.mysql.com/downloads)

MySQL 是典型的 C/S 架构：`mysql` / `mysqld`

![20220809201603](http://image.zuoright.com/20220809201603.png)

## 下载安装

### Mac

M1+ 下载ARM版本，反之x86，No thanks, just start my download，安装一直下一步即可，最后会默认创建 root 账户，让你设置一个密码（最少8位，比如：12345678）

加密方式

- `mysql_native_password` 使用明文（Use Legacy Password Encryption）
- `caching_sha2_password`，8.0版本新增，某些第三方客户端连接数据库时不兼容新密码验证方式可能会报错

然后配置环境变量

```shell
vim ~/.bash_profile
    export PATH=$PATH:/usr/local/mysql/bin
    export PATH=$PATH:/usr/local/mysql/support-files
source ~/.bash_profile
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

- `MYSQL_HOME`: `安装路径`
- `Path`: `%MYSQL_HOME%\bin`

然后以管理员身份打开命令行，输入：`mysqld --initialize --console`，初始化完毕后会生成一个初始密码（记得保存）

然后输入：`mysqld install`，注册服务，成功后即可启动服务登录使用

### Docker

镜像

- Docker官方提供的（推荐）：<https://hub.docker.com/_/mysql>
- Oracle官方提供的mysql-server（只适用于linux）：<https://hub.docker.com/r/mysql/mysql-server>

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

## 服务端

- 启动/停止服务

```shell
# Windows
net start/stop mysql

# Mac
sudo mysql.server start/stop
# 也可以通过系统设置面板控制
# 如果是homebrew安装也可以通过brew services控制
```

mysqld 可分为三层

- 连接层：负责跟客户端建立连接、获取权限、维持和管理连接，客户端发送 SQL 至服务器端
- SQL 层：对 SQL 语句进行查询处理
- 存储引擎层：与数据库文件打交道，负责数据的存储和读取

MySQL的存储引擎采用了插件形式，允许开发人员设置自己的存储引擎，强大的是每个表可以根据实际的数据处理需要采用不同的存储引擎

- InnoDB 存储引擎：MySQL 5.5 版本之后默认的存储引擎，最大的特点是支持事务、行级锁定、外键约束等。
- MyISAM 存储引擎：MySQL 5.5 版本之前默认的存储引擎，最大的特点是速度快，占用资源少，但不支持事务，也不支持外键。
- Memory 存储引擎：使用系统内存作为存储介质，以便得到更快的响应速度。不过如果 mysqld 进程崩溃，则会导致所有的数据丢失，因此我们只有当数据是临时的情况下才使用 Memory 存储引擎。
- NDB 存储引擎：也叫做 NDB Cluster 存储引擎，主要用于 MySQL Cluster 分布式集群环境，类似于 Oracle 的 RAC 集群。
- Archive 存储引擎：它有很好的压缩机制，用于文件归档，在请求写入时会进行压缩，所以也经常用来做仓库。

SQL层与存储方式无关，结构如下

![20220809202624](http://image.zuoright.com/20220809202624.png)

- 查询缓存：MySQL8.0 之后已经抛弃了这个功能，因为查询缓存往往效率不高。
- 解析器：在解析器中对 SQL 语句进行语法和语义分析。
- 优化器：在优化器中会确定 SQL 语句的执行路径，比如是根据全表检索，还是根据索引来检索等。
- 执行器：在执行之前需要判断该用户是否具备权限，如果具备权限就执行 SQL 查询并返回结果。在 MySQL8.0 以下的版本，如果设置了查询缓存，这时会将查询结果进行缓存

## 客户端

可以通过命令行或者GUI管理工具来与服务端交互

### 命令行

```shell
# 连接服务
mysql -h 127.0.0.1 -u root -p  # 12345678
# -h 默认主机名为本机 127.0.0.1，不可以与localhost混用
#   当设置为127.0.0.1时，系统通过TCP/IP方式连接数据库
#   当设置为localhost时，系统通过socket方式连接数据库
# -u 指定用户名，默认为本机user名，-uroot 这样连着写也没有问题
# -P 默认端口号3306
# -p 带此参数表明需要密码，反之不需要，回车后密文输入

SELECT VERSION();  # 查看版本
SELECT NOW()  # 查看当前时间

show databases;  # exit
create database;
use demo;
show tables;

# 断开连接
exit  # 或者quit
```

### GUI管理工具

```text
官方：Workbench
免费：DBeaver、SQLyog等 小巧，够用
付费：Navicat、DataGrip 强大
在线：phpMyAdmin 需要自己搭建
```
