# Jenkins

## 安装

> <https://www.jenkins.io/doc/book/installing/>

- 使用WAR包运行，由于下载的`jenkins.war`内置了Jetty，所以可以直接运行：`[nohub] java -jar jenkins.war [--httpPort=8080]`，然后访问：<http://localhost:8080/>，也可以使用Tomcat等其他Java Servlet容器运行
- 使用Docker镜像运行

Jenkins相关的配置等会存放在`~/.jenkins`路径下

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
