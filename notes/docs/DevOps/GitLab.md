# GitLab CI/CD

![20210808174712](http://image.zuoright.com/20210808174712.png)

> 配置文件：[`.gitlab-ci.yml`](https://docs.gitlab.com/ee/ci/yaml) (YAML语法)

## 流水线 Pipline

```yaml
# 镜像
image: registry.example.com/k8-deploy:latest
# 告诉运行器需要额外的镜像
services:
  - postgres
# 变量，也可以在项目>设置>CI/CD>变量中定义
variables:
  POSTGRES_DB: rails-sample-1_test

# 通过存储项目依赖项在job与stage之间传递信息
cache:
  paths:
    - binary/
    - .config

# stages(阶段)：定义了何时以及如何运行jobs，将jobs进行逻辑划分，每个stage可以包含多个job，同阶段并行执行，并且通常全都成功后才能执行下一阶段
stages:
  - build
  - test
  - deploy

# jobs(作业)：定义了想要在Pipeline中完成的任务，由runner(运行器)执行
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
  after_script:  # job后执行（但会在上传产物前执行），失败也会执行
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

### 预定义变量

- CI_COMMIT_BRANCH 提交分支名称。 在分支流水线中可用，包括默认分支的流水线。 在合并请求(MR)或标签(tag)流水线中不可用。
- CI_COMMIT_REF_NAME 提交分支或标签(tag)的名称。
- CI_COMMIT_TAG 提交标签名称。 仅在标签流水线中可用
- CI_REGISTRY_IMAGE 当前项目的容器镜像仓库的地址。
- CI_REGISTRY 极狐GitLab镜像仓库的地址。 如果在镜像仓库配置中指定了端口值，则此变量包括一个 :port 值
- CI_REGISTRY_USER 将容器镜像推送到项目的GitLab容器镜像仓库的用户名。
- CI_REGISTRY_PASSWORD 将容器镜像推送到项目的GitLab容器镜像仓库的密码。

## 流水线触发器(trigger)

```bash
curl -X POST \
  -F token=<TOKEN> \
  -F ref=<REF_NAME> \
  -F "variables[key]=value"  # 传递变量，gitlab-ci.yml中可以通过${key}来引用
  https://gitlab.example.com/api/v4/projects/344/trigger/pipeline
```

```yaml
trigger_pipeline:
  stage: deploy
  script:
    - "curl -X POST -F token=<TOKEN> -F ref=<REF_NAME> https://gitlab.example.com/api/v4/projects/344/trigger/pipeline"
```

```python
import requests

url = "https://gitlab.example.com/api/v4/projects/344/ref/<REF_NAME>/trigger/pipeline?token=<TOKEN>"
url_with_variables = url + "&variables[key]=value"
requests.post(url)
```
