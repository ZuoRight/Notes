# 缺陷管理

- Issue
- Redmine
- 禅道
- Jira
- 大厂：tapd(腾讯)、aone(阿里)、icafe(百度)

大厂协作软件一般都会用自建的工具，做得好之后会开源或者商业化，比如tapd现在很多公司在用

## Gitlab Issues

此项目用于缺陷生命周期管理，起名为 Bug Free 寓意缺陷越来越少

### 流程

1. QA: 新建问题
    - 分配给自己，可标记为：`New`
    - 分配给对应开发人员，可标记改为：`Open`
2. RD: 开发人员收到问题后
    - 如果认为这不是一个问题，可标记为：`Rejected`，并指回给测试人员
    - 如果选择延期修改，可标记为：`Delay`
3. RD: 开发人员解决问题后，可标记为：`Fixed`，并指回给测试人员
4. QA: 测试人员验证问题是否修复
    - 未修复则改为：`Reopen`
    - 已修复则改为：`Closed`，并关闭Issue

### 分类管理

- `Labels` 标识不同优先级、不同状态、不同交易所等
- `Milestone` 可以对单个Project的issues进行分类管理
- `Epics` 可以对同Groups下的多个Projects的issues进行分类管理

bug_free 这个项目的 issues 用于统一管理所有的 bug，选择 Milestone + Labels 来作为分类管理即可

### 缺陷模版

可在 `.gitlab/issue_templates/` 路径下创建一些缺陷模版

在创建Issue时可以直接选择使用模版，节省一些重复工作
