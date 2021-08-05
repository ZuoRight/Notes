# Jenkins

> 用户指南：<https://www.jenkins.io/zh/doc/pipeline/tour/getting-started/>
>
> Docker部署Jenkins：<https://github.com/jenkinsci/docker>
>
> 配置文件：[`Jenkinsfile`](https://www.jenkins.io/zh/doc/book/pipeline/jenkinsfile/) ([Groov语法](http://groovy-lang.org/))

## 流水线 Pipeline

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
