# GitLab

## Snippets

存放代码片段

## CI/CD

> [与Github Actions的对比](https://docs.github.com/cn/actions/learn-github-actions/migrating-from-gitlab-cicd-to-github-actions)

- pipline
- jobs(作业)：定义了想要在Pipeline中完成的任务，由runner(运行器)执行
- stages(阶段)：定义了何时以及如何运行jobs，将jobs进行逻辑划分，每个stage可以包含多个job，同一stage并行执行，并且通常全都成功后才能执行下一阶段
- script

配置文件：[.gitlab-ci.yml](https://docs.gitlab.com/ee/ci/yaml/)

```yaml
# 镜像
image: registry.example.com/k8-deploy:latest
# 告诉运行器需要额外的镜像
services:
  - postgres
# 变量，在项目>设置>CI/CD>变量中定义
variables:
  - POSTGRES_DB: rails-sample-1_test

# 通过存储项目依赖项在job与stage之间传递信息
cache:
  paths:
    - binary/
    - .config

# 阶段
stages:
  - build
  - test
  - deploy

# job
deploy-code:
  stage: deploy  # 指定阶段
  script:  # 脚本
    - command deploy

  before_script:  # 脚本前执行
    - echo $CI_BUILD_STAGE
    - apt-get update
    - apt-get install node-js -y
    - bundle install
    - npm install
  after_script:  # 脚本后执行，失败也会执行
    - rm temp/*.tmp

  # 指定专有runner
  tags:
    - osx
    - ios

  # 环境关键字：定义了应用程序的部署位置
  environment:
    name: prod
    url: http://$CI_PROJECT_NAME.$KUBE_DOMAIN
  # 何时执行
  when: manual  # 手动触发

  # 限制分支，不常用，推荐使用rules限制
  only:  # 执行
    - branches  # 所有分支
  except:  # 不执行
    - main

  # 更强大的规则限制
  rules:
    - if: '$CI_COMMIT_REF_NAME == "main"'
      when: never
    - when: always
```

- artifacts、dependencies

```yaml
build:osx:
    stage: build
    script: make build:osx
    artifacts:  # 保存制品
        paths: 
            - binaries/

test:osx:
    stage: test
    script: make test:osx
    dependencies:  # 从指定job获取制品
        - build:osx
```

- needs

允许无序执行作业，在DAG中使用

```yaml
linux:build:
    stage: build

linux:rspec:
    stage: test
    needs: ["linux:build"]

linux:rubocop:
    stage: test
    needs: ["linux:build"]
```

- parallel

设置job并行数，必须在2~50间

```yaml
test:
    parallel: 3
    script: - bundle
```

- trigger 定义下游 Pipline 触发器

```yaml
rspec:
    stage: test
    script: bundle exec rspec

staging:
    stage: deploy
    trigger:
        project: my/deployment
        branch: stable
```

- include 包含local/file/remote/template的.yml文件
- extends 加载重复配置（也可以用锚点实现，但不易读）

![20210727235201](https://i.loli.net/2021/07/27/hgUXzVNYCoyKFBd.png)

- anchors(锚点)

![20210727235245](https://i.loli.net/2021/07/27/jnvDgcEYkG8KZqx.png)