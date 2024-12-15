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

> 官网下载页面：<https://www.jenkins.io/zh/download/>

`jenkins.war` 内置了 Winstone（一个 Jetty servlet 容器包装器），所以可以直接运行

```shell
java -jar jenkins.war
# 指定端口，默认 --httpPort=8080
# 相关的配置等会存放在 ~/.jenkins 路径下
```

### Docker

- 运行 docker in docker

为了在 Jenkins 容器内执行 Docker 命令，需要在 Docker 中运行一个专用于 Jenkins 的 Docker 容器，即：`docker:dind`

> <https://hub.docker.com/_/docker>

先创建个网络：`docker network create jenkins`

```shell
docker run \ 
  --name jenkins-docker \  # 指定容器名称
  --rm \  # 关闭时自动删除容器
  --detach \  # 后台运行
  --privileged \  # 目前，在 Docker 中运行 Docker 需要特权访问才能正常运行，应谨慎使用，因为它提供了对主机环境的完全访问权限
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

- 运行 Jenkins 容器

[官方镜像](https://hub.docker.com/r/jenkins/jenkins/)：`jenkins/jenkins`

该镜像不包含 Docker CLI，并且未捆绑常用的 Blue Ocean 插件及其功能

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
  --volume jenkins-data:/var/jenkins_home \  # 创建或使用一个名为jenkins-data的Docker卷，挂载到容器内的/var/jenkins_home目录
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

在 Jenkins 容器中安装工具

```shell
docker exec -it -u root jenkins bash  # 以root身份进入容器
apt update

# 安装 busybox
apt install busybox  # busybox ping 172.18.0.3

# 或者安装 ping
apt install iputils-ping
```

## 初始化

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

> 也可以跳过后面再安装，吐槽下：Jenkins 插件管理页面搜索时，如果搜不到插件，可能是左边的 Tab 定位在了 Updates，此时就搜不到可用插件

![20240728220421](https://image.zuoright.com/20240728220421.png)

安装完插件后会自动出现 Create First Admin User 页面，也可以选择使用 admin 账户继续

> 设置了新管理员并完成初始化后，admin 账户将失效

![20240728220756](https://image.zuoright.com/20240728220756.png)

![20240728221117](https://image.zuoright.com/20240728221117.png)

设置完后便会跳转到我们常见的 Dashboard 页面

![20240728221907](https://image.zuoright.com/20240728221907.png)

![20240728225916](https://image.zuoright.com/20240728225916.png)

## 基础用法

新建任务（Job），选择「构建一个自由风格的软件项目」，输入项目名称，确定后跳转配置页面

- Build Steps

选择无源码，直接增加构建步骤，选择「执行 shell」，输入 `echo "Hello, World!"`，保存后，立即构建，即可在控制台输出中看到结果

![20241208204427](https://image.zuoright.com/20241208204427.png)

### 源码管理

![20210810141105](http://image.zuoright.com/20210810141105.png)

如果是私有仓库，则需要添加凭证：<http://localhost:8080/manage/credentials/>

![20210810140805](http://image.zuoright.com/20210810140805.png)

```text
-----BEGIN OPENSSH PRIVATE KEY-----
配置私钥时上下这两句话也记得复制，而不要只复制中间的部分
-----END OPENSSH PRIVATE KEY-----
```

注意，首次连接，[全局安全配置](http://localhost:8080/manage/configureSecurity/)这里如果没选 `Accept first connection` 会找不到 known_hosts 中对应的 key

![20241208215730](https://image.zuoright.com/20241208215730.png)

### 配置 Allure

- 系统管理 -> 全局工具配置 -> Allure Commandline

![20210810141945](http://image.zuoright.com/20210810141945.png)

- 构建及输出报告配置

![20210810141715](http://image.zuoright.com/20210810141715.png)

- 立即构建

![20210810142615](http://image.zuoright.com/20210810142615.png)

## Pipeline

Jenkins Pipeline 是一套插件，支持在 Jenkins 中实施和集成持续交付管道（从版本控制到用户获取软件的自动化流程）

新建任务（Job），选择「流水线」，输入项目名称，确定后跳转配置页面

![20241209004952](https://image.zuoright.com/20241209004952.png)

在项目根路径下创建 [Jenkinsfile](https://www.jenkins.io/zh/doc/book/pipeline/jenkinsfile/) 文件

- Node 执行 Pipeline 的机器
- Pipeline 整个构建过程
- Job 整个任务
- Stage 任务子集，比如不同阶段：Build, Test, Deploy
- Step 单个任务

Jenkinsfile 语法基于 [Groovy](http://groovy-lang.org/) 的 DSL

### 脚本式 Scripted

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

### 声明式 Declarative

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
