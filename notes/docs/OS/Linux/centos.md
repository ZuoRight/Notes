# CentOS

> 包管理器：`yum`（Yellow dog Updater, Modified）
>
> 安装包格式：`name-软件版本-系统版本.平台.rpm`

- 网络安装

> 配置文件：`/etc/yum.repos.d/CentOS-Base.repo`

```bash
yum search/list/install/remove/update xxx
```

- 本地安装

官网找`·.rpm`安装包，然后挂载到空目录

```bash
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

rpm -i /mnt/name-xxx.rpm  # 安装install
rpm -qa | more  # 查询所有已安装的软件包
rpm -qi name  # 查询指定安装包信息
rpm -e name  # 卸载
```
