# Jenkins

## 安装

参考：<https://www.jenkins.io/doc/book/installing/>

### WAR file

Jenkins Web 应用程序 ARchive (WAR) 文件捆绑了 

`jenkins.war` 内置了 Winstone（一个 Jetty servlet 容器包装器）

所以可以直接运行

```shell
java -jar jenkins.war
# 指定端口，默认 --httpPort=8080
# 相关的配置等会存放在 ~/.jenkins 路径下
```

然后访问：<http://localhost:8080/> 等待 Unlock Jenkins 页面出现

![20240725231817](https://image.zuoright.com/20240725231817.png)

获取 Adminstrator password

```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

解锁后会出现 Customize Jenkins 页面，可以安装各种插件

安装完插件后会出现 Create First Admin User 页面，设置完便可以使用了

### Docker

```shell
mkdir jenkins
chmod 777 jenkins

docker run -d --name jenkins \
-p 8080:8080 \  # 8080 为 Jenkins 服务 Web 端口
-p 50000:50000 \  # 50000 为 Jenkins 和其他节点通讯用的端口
-v ${PWD}/jenkins:var/jenkins_home \
jenkins/jenkins:lts

# 查看初始化密码
docker logs -f jenkins  # 方式1，从日志中查看
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword  # 方式2，从容器初始密码文件中看
cat jenkins/jenkins_home/secrets/initialAdminPassword  # 方式3，直接从宿主机挂载目录查看
```

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

## 流水线 Pipeline

> [`Jenkinsfile`](https://www.jenkins.io/zh/doc/book/pipeline/jenkinsfile/) ([Groov语法](http://groovy-lang.org/))

- 声明式流水线：Declarative Pipeline

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

- 脚本式流水线：Scripted Pipeline

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
