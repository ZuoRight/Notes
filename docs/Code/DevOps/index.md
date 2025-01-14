---
comments: true
hide:
  - feedback
---

# 引言

DevOps(Development and Operations) 是一种文化、运动或实践，提倡尽可能地自动化软件开发和部署流程。

DevOps 依赖一系列工具来实现其目标，包括版本控制系统、自动化部署工具、配置管理工具、监控服务等

- CI(Continuous Integrationv) 持续集成
- CD(Continuous Deploymen) 持续交付

![20240522103905](https://image.zuoright.com/20240522103905.png)

![20210808174712](https://image.zuoright.com/20210808174712.png)

## VCS

Version Control System 版本控制系统

用于跟踪文本文件的改动，对于二进制文件只能跟踪其大小的变化

- 文本文件：各种源代码、纯文本等
- 二进制文件(Blob, binary large object)：图片、视频、office文件（其实是个压缩包，由很多.xml和其他文件构成，所以属于二进制）

VCS 主要分为

- 本地版本控制系统，比如：RCS
- 集中式版本控制系统，比如：SVN
- 分布式版本控制系统，比如：Git

## 代码托管平台

- GitHub：现已被微软收购，最大的托管平台
- GitLab：乌克兰企业，在中国创建了合资品牌（极狐），主要以开源版本著称，组织可以私有化部署，当然也提供云托管服务
- Bitbucket：Jira母公司Atlassian旗下产品
- Gitee(码云)：开源中国和工信部联合推出
- Coding：并购了Gitcafe，后又被腾讯云全资收购
