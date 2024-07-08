# Charles

对于工具的使用，最好的学习方式就是看[官方文档](https://www.charlesproxy.com/documentation/tools/)

- Structure 网络请求按访问域名分类
- Sequence 网络请求按访问时间排序
- Focus 聚焦显示想看的域名，其它域名归类到其它

## 配置代理

Tips：端口号随意，通常设为 8888

![20200802230557](http://image.zuoright.com/20200802230557.png)

## 抓取 HTTP 包

### 代理所有流量

这样系统所有流量都会走代理，不推荐

![20230814230019](https://image.zuoright.com/20230814230019.png)

### 代理 Web

可以使用浏览器插件SwitchyOmega代理到8888端口，代理协议选HTTP

![20220826173141](http://image.zuoright.com/20220826173141.png)

或者勾选 Proxy 选项中的 `Windows/macOS Proxy`

> 每次重启需要重新勾选，当然可以设置启动时自动代理

![20200803232853](http://image.zuoright.com/20200803232853.png)

### 代理 APP

Tips：

1. 需要将手机和电脑保持在同一局域网内，然后手动配置下相应网络的 HTTP 代理
2. 保存后，Charles 界面会弹出确认框，点击允许（若没弹框可以重启下 Charles 试试）

![lALPDhYBNT2oVhrNA1LNBsY_1734_850](http://image.zuoright.com/lALPDhYBNT2oVhrNA1LNBsY_1734_850.png)

![lADPDhJztpjkh8HNBTbNAu4_750_1334](http://image.zuoright.com/lADPDhJztpjkh8HNBTbNAu4_750_1334.jpg)

## 抓取 HTTPS 包

1. 配置 SSL 代理，端口号为 443
2. 根据不同操作系统安装相应证书

![20200802230833](http://image.zuoright.com/20200802230833.png)

### Windows 安装证书

![20200802224500](http://image.zuoright.com/20200802224500.png)

![20200802224002](http://image.zuoright.com/20200802224002.png)

### Mac 安装证书

![lALPDgfLOqpB3xzNAvLNCFg_2136_754](http://image.zuoright.com/lALPDgfLOqpB3xzNAvLNCFg_2136_754.png)

![lALPDgfLOqvqYXzNBgTNCC4_2094_1540](http://image.zuoright.com/lALPDgfLOqvqYXzNBgTNCC4_2094_1540.png)

### iOS 安装证书

1. 手机网络配置代理后，浏览器访问 `chls.pro/ssl`，下载证书
2. 打开设置 > 通用 > 描述文件 > 点击 Charles 证书 > 安装
3. 打开设置 > 关于本机 > 证书信任设置 > 找到 Charles 证书 > 启用信任

![lADPDgQ9vAXnSl_NBQDNAtA_720_1280](http://image.zuoright.com/lADPDgQ9vAXnSl_NBQDNAtA_720_1280.jpg)

### Android

查看 Android Tools

## External Proxy

如果要抓包的网站，本身就需要代理才能访问，则可以先用 Charles 反向代理后，再充当前置代理将流量转出

![20240706143357](https://image.zuoright.com/20240706143357.png)

Socket 或 HTTP 都可以，根据自己的代理进行选择，若选了 HTTP 通常还需要勾选上 HTTPS，如果不清楚就都配置上

## Breakpoints

打断点，修改请求或返回内容

## Rewrite

跨域会先发一次 OPTIONS 请求，需要先改写返回头。

rewrite rule

```json
{
    "Access-Control-Allow-Origin": "https://xx.xxx.com",
    "Access-Control-Allow-Credentials": true
}
```

add location

注意

- 此时如果开浏览器控制台不要勾选「Disable cache」，否则请求会出错。
- 另外 path 末尾不要带空格，否则匹配不上。

## Map Remote

请求重定向，在不修改本地代码的情况下重定向网络流量到测试或开发环境

比如前端改动了一些东西，在发布之前重定向到线上，用真实数据填充查看下效果

## Map Local

将网络请求重定向或者说映射到本地，模拟返回内容

## Bandwidth Throttling

模拟弱网
