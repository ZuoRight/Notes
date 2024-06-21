# 配置

## 拉取镜像使用mirror加速

Docs: 配置[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

- Linux

> <https://docs.docker.com/config/daemon/>

```shell
cd /etc/docker
vim daemon.json  # 添加 registry-mirrors 配置
sudo systemctl daemon-reload  
sudo systemctl restart docker  
```

- Mac/Windows

![20210726135953](http://image.zuoright.com/20210726135953.png)

## 拉取镜像时使用代理

> <https://docs.docker.com/config/daemon/systemd/>

创建`proxy.conf`文件

```shell
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo vim /etc/systemd/system/docker.service.d/proxy.conf
```

设置代理

```yaml
[Service]
Environment="ALL_PROXY=socks5://127.0.0.1:1081"
```

重启生效

```shell
sudo systemctl daemon-reload  # 重新加载配置文件
sudo systemctl restart docker  # 重启docker
sudo systemctl show --property=Environment docker  # 验证
```

## 容器中运行使用代理

- 方式1

1. 查看docker在宿主机的虚拟网卡（`docker0`）的IP：`ip addr show docker0`，通常为：`172.0.0.1`
2. 容器内设置全局代理到docker0：`export all_proxy="socks5://172.0.0.1:1081"`

> 注意：在Windows和macOS平台下并没有docker0虚拟网卡，这时候可以使用host.docker.internal这个特殊的DNS名称来解析宿主机IP

- 方式2（推荐）

1. 容器网络设置为：`--network host` 或者 `-p 1081:1081`
2. 容器内设置全局代理

```shell
export all_proxy="socks5://127.0.0.1:1081"
alias proal1='export http_proxy=http://127.0.0.1:8123 https_proxy=http://127.0.0.1:8123'
alias proal0='unset http_proxy https_proxy'
```

## 构建镜像使用代理

- 方式1

`docker build --build-arg all_proxy="socks5://127.0.0.1:1081"`

- 方式2

> <https://docs.docker.com/engine/reference/commandline/cli/#configuration-files>

`~/.docker/config.json`

```json
{
    "proxies": {
        "default": {
            "allProxy": "socks5:://127.0.0.1:1081"
        }
    }
}
```
