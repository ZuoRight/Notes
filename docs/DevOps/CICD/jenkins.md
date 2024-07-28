# Jenkins

官方文档：<https://www.jenkins.io/doc/>

## 安装

以 Mac 为例

### Homebrew

```shell
brew install jenkins-lts
brew services start jenkins-lts

# 获取 Adminstrator password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### WAR File

Jenkins Web 应用程序 ARchive (WAR) 文件捆绑了 

`jenkins.war` 内置了 Winstone（一个 Jetty servlet 容器包装器）

所以可以直接运行

```shell
java -jar jenkins.war
# 指定端口，默认 --httpPort=8080
# 相关的配置等会存放在 ~/.jenkins 路径下
```

### Docker

[官方镜像](https://hub.docker.com/r/jenkins/jenkins/)：`jenkins/jenkins`

该镜像不包含 Docker CLI，并且未捆绑常用的 Blue Ocean 插件及其功能

为了在 Jenkins 容器内执行 Docker 命令，需要在 Docker 中运行一个专用于 Jenkins 的 Docker 容器，即：`docker:dind`

先创建个网络：`docker network create jenkins`

```shell
docker run \ 
  --name jenkins-docker \  # 指定容器名称
  --rm \  # 关闭时自动删除容器
  --detach \  # 后台运行
  --privileged \  # 需要特权才能访问 DinD
  --network jenkins \  # 指定网络，以便于 Jenkins 主容器和 DinD 之间的通信
  --network-alias docker \  # 为 DinD 指定网络别名为 docker，方便在同一网络内的其他容器通过该别名访问它
  --env DOCKER_TLS_CERTDIR=/certs \  # 允许在 Docker 服务器中使用 TLS
  --volume jenkins-docker-certs:/certs/client \ 
  --volume jenkins-data:/var/jenkins_home \ 
  --publish 2376:2376 \  # 映射端口
  docker:dind \ 
  --storage-driver overlay2

# 无注释版，方便粘贴
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind --storage-driver overlay2

# 可手动停止容器
docker stop jenkins-docker
```

运行 Jenkins 容器

```shell
docker run \ 
  --name jenkins \  # 指定容器名称
  --restart=on-failure \  # 失败时自动重启
  -d \  # 后台运行
  --network jenkins
  --env DOCKER_HOST=tcp://docker:2376 \ 
  --env DOCKER_CERT_PATH=/certs/client \ 
  --env DOCKER_TLS_VERIFY=1 \ 
  -p 8080:8080 \  # 8080 为 Jenkins 服务 Web 端口
  -p 50000:50000 \  # 50000 为 Jenkins 和其他节点通讯用的端口
  --volume jenkins-docker-certs:/certs/client:ro \ 
  --volume jenkins-data:/var/jenkins_home \ 
  jenkins/jenkins:lts

# 无注释版，方便复制粘贴
docker run --name jenkins --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins/jenkins:lts
```

然后访问：<http://localhost:8080/> 等待 Unlock Jenkins 页面出现

![20240725231817](https://image.zuoright.com/20240725231817.png)

查看初始化密码

```shell
# 方式1，从日志中查看
docker logs -f jenkins
'
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

7ecc70bcb1914691a63dd904804cca75

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
'

# 方式2，从容器初始密码文件中看
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword

# 方式3，直接从宿主机挂载目录查看
cat jenkins-data/secrets/initialAdminPassword
```

解锁后会出现 Customize Jenkins 页面，可以选择「安装推荐的插件」

![20240728220421](https://image.zuoright.com/20240728220421.png)

安装完插件后会自动出现 Create First Admin User 页面，也可以选择使用 admin 账户继续

![20240728220756](https://image.zuoright.com/20240728220756.png)

![20240728221117](https://image.zuoright.com/20240728221117.png)

设置完后便会跳转到我们常见的 Dashboard 页面

![20240728221907](https://image.zuoright.com/20240728221907.png)

![20240728225916](https://image.zuoright.com/20240728225916.png)

## Demo

- 源码管理

![20210810141105](http://image.zuoright.com/20210810141105.png)

- 配置 Git 私钥

![20210810140805](http://image.zuoright.com/20210810140805.png)

- 配置Allure：系统管理/全局工具配置/Allure Commandline

![20210810141945](http://image.zuoright.com/20210810141945.png)

- 构建及输出报告配置

![20210810141715](http://image.zuoright.com/20210810141715.png)

- 立即构建

![20210810142615](http://image.zuoright.com/20210810142615.png)

## Pipeline

Jenkins Pipeline 是一套插件，支持在 Jenkins 中实施和集成持续交付管道（从版本控制到用户获取软件的自动化流程）

- Node 执行 Pipeline 的机器
- Pipeline 整个构建过程
- Job 整个任务
- Stage 任务子集，比如不同阶段：Build, Test, Deploy
- Step 单个任务

Pipeline 使用基于 [Groovy](http://groovy-lang.org/) 的 DSL 定义在 [Jenkinsfile](https://www.jenkins.io/zh/doc/book/pipeline/jenkinsfile/) 中

Jenkinsfile 可以使用两种类型的语法编写

- 脚本式 Scripted

```groovy
node {  // 分配执行器和工作区
    stage('Build') {
        echo 'Building....'
    }
    stage('Test') {
        echo 'Testing....'
    }
    stage('Deploy') {
        echo 'Deploying....'
    }
}
```

- 声明式 Declarative

比脚本式语法更丰富，更易编写和阅读

```groovy
pipeline {
    agent any  // 分配执行器和工作区，必须

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```
