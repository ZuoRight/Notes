# Docker 搭建常用环境

- Tomcat

```bash
docker run --name tomcat2 -p 8080:8080 -v $PWD/test:/usr/local/tomcat/webapps/test -d tomcat
# docker镜像默认webapps是空的，把webapps.dist中的ROOT文件夹复制或者移动到webapps下即可
# 如果需要永久生效需要docker commit重新提交一个修改后的镜像，不然下次启动又会变回空的
```

- Nginx

```bash
# 编辑.html文件
vi ./html/index.html
# 启动服务，并将html目录挂载到容器
docker run -d --name nginx -p 8088:80 -v ${PWD}/html:/usr/share/nginx/html nginx
```

- TestLink

```bash
# 新建容器网络testlink-tier
docker network create testlink-tier
# 启动MariaDB数据库服务
docker run -d --name mariadb \  # 后台服务方式执行，容器名：mariadb
  -e MARIADB_DATABASE=bitnami_testlink \  # 参数，数据库名：bitnami_testlink
  -e MARIADB_ROOT_PASSWORD=mariadb \  # 管理员密码
  -e MARIADB_USER=bn_testlink \  # 参数，创建一个用户：bn_testlink
  -e MARIADB_PASSWORD=bn_testlink \  # 参数，设置密码：bn_testlink
  --net testlink-tier \  # 指定要使用的网络（上面创建的）
  -v ${HOME}/docker/mariadb:/bitnami \  # 挂载宿主机目录到容器内/bitnami目录
  bitnami/mariadb  # 要启动的容器
# 启动testlink，默认账号：user，密码：bitnami
docker run -d --name testlink -p 8080:8080 -p 8443:8443 \  # 分别映射http和https的端口
  -e TESTLINK_DATABASE_NAME=bitnami_testlink \  # 数据库
  -e TESTLINK_DATABASE_USER=bn_testlink \  # 用户名
  -e TESTLINK_DATABASE_PASSWORD=bn_testlink \  # 密码
  --net testlink-tier \  # 与数据库使用同一网络
  -v ${PWD}/testlink:/bitnami \
  bitnami/testlink
```

- Jenkins

```bash
mkdir jenkins
chmod 777 jenkins
docker run -d --name jenkins \
  -p 8080:8080 -p 50000:50000 \  # 8080为jenkins服务web端口，50000为jenkins和其他节点通讯用的端口
  -v ${PWD}/jenkins:var/jenkins_home \
  jenkins/jenkins
# 查看初始化密码
docker logs -f jenkins  # 方式1，从日志中查看
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword  # 方式2，从容器初始密码文件中看
cat jenkins/jenkins_home/secrets/initialAdminPassword  # 方式3，直接从宿主机挂载目录查看
```
