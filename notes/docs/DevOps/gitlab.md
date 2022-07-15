# GitLab CI

- CI(Continuous Integrationv) 持续集成
- CD(Continuous Deploymen) 持续部署/交付

![20210808174712](http://image.zuoright.com/20210808174712.png)

## 安装Runner

> [官方文档](https://docs.gitlab.com/runner/install/linux-repository.html)

Docker中安装

```bash
sudo docker run -d --name gitlab-runner --restart always \
     -v /srv/gitlab-runner/config:/etc/gitlab-runner \
     -v /var/run/docker.sock:/var/run/docker.sock \
     gitlab/gitlab-runner:latest
```

作用范围

- 共享：适用于所有项目，由gitlab管理员管理
- 专用：适用于特定项目，由runner所有者管理

安全

- 受保护
- 不受保护

标签

- 有标签
- 无标签

## 注册runner

![20220305002455](http://image.zuoright.com/20220305002455.png)

- 命令行自动注册

```bash
# 注册
gitlab-runner register
"""
根据提示
输入url https:/code.xxx.net/
输入认证token：registration token
输入描述（可默认，runner名）
输入标签（可默认）
输入最大进程数（可默认）
输入执行器：
  docker 最常用，一般选这个
  docker machine 云部署中较常用
  kubernetes K8s集群中作为Pod运行
  其它不常用的
    shell
    ssh
    virtualbox
    parallels
如果执行器是docker需要输入默认镜像：alpine:latest
"""
# 验证
gitlab-runner status
gitlab-runner verify
```

- 手动配置文件注册

> 配置说明：[官方文档](https://docs.gitlab.com/runner/configuration/advanced-configuration.html)
>
> You can find the `config.toml` file in:
>
> - `/etc/gitlab-runner/` on *nix systems when GitLab Runner is executed as root (this is also the path for service configuration)
> - `~/.gitlab-runner/` on *nix systems when GitLab Runner is executed as non-root
> - `./` on other systems
>
> 修改配置后不需要重启，Gitlab Runner每3s会自动检查配置并自动加载更新
> Runner如果是在Docker中，配置文件可挂载在：`srv/gitlab-runner/config/config.toml`

```toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "ee_rpc_test ci"
  url = "https:/code.xxx.net/"
  token = "Authentication token"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache","/var/run/docker.sock:/var/run/docker.sock"]
    shm_size = 0
```

> `volumes`字段与`docker -v`语法相同，`:ro`(只读)或`:rw`(读写，默认)，需要保证容器有足够权限读写宿主机路径
>
> 注意，配置文件中的token是`Authentication token`，不走命令行的话需要使用[API](https://docs.gitlab.com/ee/api/runners.html#register-a-new-runner)生成

```bash
curl --request POST "https://gitlab.example.com/api/v4/runners" \
     --form "token=<registration_token>" --form "description=test-1-20150125-test" \
     --form "tag_list=ruby,mysql,tag1,tag2"
```

## 流水线 Pipline

> 配置文件：[`.gitlab-ci.yml`](https://docs.gitlab.com/ee/ci/yaml) (YAML语法)

```yaml
# 镜像
image: registry.example.com/k8-deploy:latest
# 告诉运行器需要额外的镜像
services:
  - postgres
variables:
  POSTGRES_DB: rails-sample-1_test

# 通过存储项目依赖项在job与stage之间传递信息，也可以用artifacts
cache:
  paths:
    - binary/
    - .config

# stages(阶段)：定义了何时以及如何运行jobs，将jobs进行逻辑划分
# 不同阶段属于不同的容器
# 每个stage可以包含多个job，同阶段并行执行，并且通常全都成功后才能执行下一阶段
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

  # 限制分支，不常用，推荐使用rules限制
  only:  # 执行
    - branches  # 所有分支
  except:  # 不执行
    - main
```

- when

何时执行: <https://docs.gitlab.com/ee/ci/yaml/#when>

```yaml
when: on_success  # 默认，仅在早期阶段的所有作业都成功或具有allow_failure: true时执行
when: always  # 无论早期阶段的作业状态如何，都运行作业。也可用于workflow:rules
when: on_failure  # 仅当至少一个早期阶段的作业失败时才运行作业
when: manual  # 仅在手动触发时运行作业
when: delayed  # 将作业的执行延迟指定的持续时间
when: never  # 不要运行作业，只能在rules或workflow: rules中使用
```

- rules

更强大的规则限制，不能与only/except共用: <https://docs.gitlab.com/ee/ci/yaml/#rules>

```yaml
rules:
  - if: $CI_COMMIT_REF_NAME == "main"
    when: never
```

- artifacts / dependencies

缓存或者临时文件的存储

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

> 注意：needs需要的是job名，不是阶段名

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

![20220715185455](http://image.zuoright.com/20220715185455.png)

- anchors(锚点)

![20220715185639](http://image.zuoright.com/20220715185639.png)

## 变量

### 自定义变量

<https://docs.gitlab.com/ee/ci/variables/index.html#custom-cicd-variables>

设置变量的方式

- `.gitlab-ci.yml`
- 可以在项目`Settings > CI/CD`的`Variables`部分定义
- 在群组配置中定义
- 在实例中定义（admin用户）
- 在手工触发时定义
- 在API触发时定义

```text
Key: Must be one line, with no spaces, using only letters, numbers, or _.
Value: No limitations.
Type: File or Variable.
Environment scope: All, or specific environments.
Protect variable (Optional): If selected, the variable is only available in pipelines that run on protected branches or tags.
Mask variable (Optional): If selected, the variable's Value is masked in job logs. The variable fails to save if the value does not meet the masking requirements.
```

### 预定义变量

<https://docs.gitlab.com/ee/ci/variables/predefined_variables.html>

- CI_COMMIT_BRANCH 提交分支名称。 在分支流水线中可用，包括默认分支的流水线。 在合并请求(MR)或标签(tag)流水线中不可用。
- CI_COMMIT_REF_NAME 提交分支或标签(tag)的名称。
- CI_COMMIT_TAG 提交标签名称。 仅在标签流水线中可用
- CI_REGISTRY_IMAGE 当前项目的容器镜像仓库的地址。
- CI_REGISTRY 极狐GitLab镜像仓库的地址。 如果在镜像仓库配置中指定了端口值，则此变量包括一个 :port 值
- CI_REGISTRY_USER 将容器镜像推送到项目的GitLab容器镜像仓库的用户名。
- CI_REGISTRY_PASSWORD 将容器镜像推送到项目的GitLab容器镜像仓库的密码。

## 流水线触发器(trigger)

触发器传递的环境变量优先级最高

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

## 管道计划/定时任务

<https://docs.gitlab.com/ee/ci/pipelines/schedules.html>

```bash
# 文件格式說明
# ┌──分鐘（0 - 59）
# │ ┌──小時（0 - 23）
# │ │ ┌──日（1 - 31）
# │ │ │ ┌─月（1 - 12）
# │ │ │ │ ┌─星期（0 - 6，表示从周日到周六）
# │ │ │ │ │
# * * * * * 被執行的命令

# 每天下午六点
0 18 * * *
```
