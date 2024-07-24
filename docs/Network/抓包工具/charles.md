# Charles

对于工具的使用，最好的学习方式就是看 [官方文档](https://www.charlesproxy.com/documentation/tools/)

首次打开记得选择授予特权（Grant Privileges）

Charles 虽然收费，但可以免费试用 30 天，试用期过后，仍可继续使用，但每隔 30min 会强制退出，可以购买 Licenses 授权码，约 $30，填入到 Help -> Register Charles 中即可

> 有 [在线网站](https://www.zzzmode.com/mytools/charles/) 可免费生成 Licenses

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

可以使用浏览器插件 SwitchyOmega 代理到 8888 端口，代理协议选 HTTP

![20220826173141](http://image.zuoright.com/20220826173141.png)

或者勾选 Proxy 选项中的 `Windows/macOS Proxy`

> 每次重启需要重新勾选，当然可以设置启动时自动代理

![20200803232853](http://image.zuoright.com/20200803232853.png)

### 代理 APP

Tips：

1. 需要将手机和电脑保持在同一局域网内，然后手动配置下相应网络的 HTTP 代理
2. 保存后，Charles 界面会弹出确认框，点击允许（若没弹框可以重启下 Charles 试试）

> 如果手机设置了代理，Charles 却没反应，可手动将手机的局域网 IP 配置到 Proxy -> Access Control Settings 中，如果还是不行，重启手机！重启电脑！重启大法！

![lALPDhYBNT2oVhrNA1LNBsY_1734_850](http://image.zuoright.com/lALPDhYBNT2oVhrNA1LNBsY_1734_850.png)

可在 Help -> Local IP Address 中查看本机局域网 IP Address

![lADPDhJztpjkh8HNBTbNAu4_750_1334](http://image.zuoright.com/lADPDhJztpjkh8HNBTbNAu4_750_1334.jpg)

## 抓取 HTTPS 包

1. 配置 SSL 代理，Host 为 `*`，端口号为 `443` 或者也可以是 `*`
2. 根据不同操作系统安装相应证书

![20200802230833](http://image.zuoright.com/20200802230833.png)

### iOS 安装证书

1. 手机网络配置代理后，浏览器访问 `chls.pro/ssl`，下载证书
2. 打开设置 > 通用 > VPN 与设备管理 > 点击 Charles 证书 > 安装
3. 打开设置 > 关于本机 > 证书信任设置 > 找到 Charles 证书 > 启用信任

![lADPDgQ9vAXnSl_NBQDNAtA_720_1280](http://image.zuoright.com/lADPDgQ9vAXnSl_NBQDNAtA_720_1280.jpg)

### Android

以小米为例，浏览器访问 `chls.pro/ssl`，下载证书

或者 Charles Help -> SSL Proxying -> Save Charles Root Certificate，使用 ADB 将证书传到手机中：`adb push path/charles-ssl-proxying-certificate.cer sdcard/`

然后在：设置 -> 安全 -> 更多安全设置 -> 更多安全设置 -> 加密与凭证 -> 安装证书 -> CA 证书，路径下找到证书并安装即可

如果是自己公司的测试包，开发修改过配置文件，是可以抓到的，否则 HTTPs 请求会显示 unknown，具体请查看 [Android 抓包](android_proxy.md) 解决

### Mac 安装证书

![lALPDgfLOqpB3xzNAvLNCFg_2136_754](http://image.zuoright.com/lALPDgfLOqpB3xzNAvLNCFg_2136_754.png)

![lALPDgfLOqvqYXzNBgTNCC4_2094_1540](http://image.zuoright.com/lALPDgfLOqvqYXzNBgTNCC4_2094_1540.png)

### Windows 安装证书

![20200802224500](http://image.zuoright.com/20200802224500.png)

![20200802224002](http://image.zuoright.com/20200802224002.png)

## External Proxy

如果要抓包的网站，本身就需要代理才能访问，则可以先用 Charles 反向代理后，再充当前置代理将流量转出

![20240706143357](https://image.zuoright.com/20240706143357.png)

Socket 或 HTTP 都可以，根据自己的代理进行选择，若选了 HTTP 通常还需要勾选上 HTTPS，如果不清楚就都配置上

## Breakpoints

打断点，修改请求或返回内容

## Bandwidth Throttling

模拟弱网

## Tools

## Map Remote

请求重定向，在不修改本地代码的情况下重定向网络流量到测试或开发环境

比如前端改动了一些东西，在发布之前重定向到线上，用真实数据填充查看下效果

## Map Local

将网络请求重定向或者说映射到本地，模拟返回内容

### Rewrite

可以根据规则重写请求或者返回内容

比如跨域会先发一次 OPTIONS 请求，需要先改写返回头。

add location

rewrite rule

```json
{
    "Access-Control-Allow-Origin": "https://xx.xxx.com",
    "Access-Control-Allow-Credentials": true
}
```

注意

- 此时如果开浏览器控制台不要勾选「Disable cache」，否则请求会出错。
- 另外 path 末尾不要带空格，否则匹配不上。
