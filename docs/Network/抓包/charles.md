# Charles

[官方文档](https://www.charlesproxy.com)

首次打开记得选择授予特权（Grant Privileges）

Charles 虽然收费，但可以免费试用 30 天，试用期过后，仍可继续使用，但每隔 30min 会强制退出，可以购买 Licenses 授权码，约 $30，填入到 Help -> Register Charles 中即可

> 有 [在线网站](https://www.zzzmode.com/mytools/charles/) 可免费生成 Licenses

## 配置代理

Tips：端口号随意，通常设为 8888

![20200802230557](https://image.zuoright.com/20200802230557.png)

## 抓取 HTTP 包

### 代理所有流量

这样系统所有流量都会走代理，不推荐

![20230814230019](https://image.zuoright.com/20230814230019.png)

### 代理 Web

可以使用浏览器插件 SwitchyOmega 代理到 8888 端口，代理协议选 HTTP

![20220826173141](https://image.zuoright.com/20220826173141.png)

或者勾选 Proxy 选项中的 `Windows/macOS Proxy`

> 每次重启需要重新勾选，当然可以设置启动时自动代理

![20200803232853](https://image.zuoright.com/20200803232853.png)

### 代理 APP

Tips：

1. 需要将手机和电脑保持在同一局域网内，然后手动配置下相应网络的 HTTP 代理
2. 保存后，Charles 界面会弹出确认框，点击允许

若没弹确认框，可手动将手机的局域网 IP 配置到 Proxy -> Access Control Settings 中

如果设置都正常但出现连不上 Charles 的情况，断开电脑网络重连一次，或者重新设置一次手机代理，实在不行使用重启大法！

![lALPDhYBNT2oVhrNA1LNBsY_1734_850](https://image.zuoright.com/lALPDhYBNT2oVhrNA1LNBsY_1734_850.png)

可在 Help -> Local IP Address 中查看本机局域网 IP Address

![lADPDhJztpjkh8HNBTbNAu4_750_1334](https://image.zuoright.com/lADPDhJztpjkh8HNBTbNAu4_750_1334.jpg)

## 抓取 HTTPS 包

1. 配置 SSL 代理，Host 为 `*`，端口号为 `443` 或者也可以是 `*`

![20200802230833](https://image.zuoright.com/20200802230833.png)

2. 根据不同操作系统安装相应证书

如果无法通过 `chls.pro/ssl` 下载证书，可以通过 Charles Help -> SSL Proxying -> Save Charles Root Certificate 然后传输到手机端安装

> 不同操作系统安装的证书是一样的，但 Charles 在不同电脑的证书是不一样的，比如一台手机要被两台电脑的 Charles 代理，需要在手机上安装两个证书

### iOS 安装证书

> 注意：手机不要开启 VPN，否则会影响代理

1. 下载：手机网络配置代理后，使用 Safari 浏览器访问 `chls.pro/ssl`，下载证书
2. 安装：根据下载时的提示，或者打开设置 > 通用 > VPN 与设备管理 > 点击 Charles 证书 > 安装
3. 信任：打开设置 > 关于本机 > 证书信任设置 > 找到 Charles 证书 > 启用信任

![20241014144545](https://image.zuoright.com/20241014144545.png)

![lADPDgQ9vAXnSl_NBQDNAtA_720_1280](https://image.zuoright.com/lADPDgQ9vAXnSl_NBQDNAtA_720_1280.jpg)

### Android

以小米为例，浏览器访问 `chls.pro/ssl`，下载证书

或者使用 ADB 将证书传到手机中：`adb push path/charles-ssl-proxying-certificate.cer sdcard/`

然后在：设置 -> 安全 -> 更多安全设置 -> 更多安全设置 -> 加密与凭证 -> 安装证书 -> CA 证书，路径下找到证书并安装即可

如果是自己公司的测试包，开发修改过配置文件，是可以抓到的，否则 HTTPs 请求会显示 unknown，具体请查看 [Android 抓包](android_proxy.md) 解决

### Mac 安装证书

![lALPDgfLOqpB3xzNAvLNCFg_2136_754](https://image.zuoright.com/lALPDgfLOqpB3xzNAvLNCFg_2136_754.png)

![lALPDgfLOqvqYXzNBgTNCC4_2094_1540](https://image.zuoright.com/lALPDgfLOqvqYXzNBgTNCC4_2094_1540.png)

### Windows 安装证书

![20200802224500](https://image.zuoright.com/20200802224500.png)

![20200802224002](https://image.zuoright.com/20200802224002.png)

## 过滤请求

### Structure

网络请求按访问域名分类

Filter 可过滤 host，比如输入 test 可过滤出所有 host 包含 test 的：xx1.test.com, xx2.test.com.cn 等

Focus 聚焦显示想看的域名，其它域名归类到 Other Hosts，Focus 多个即可以同时过滤多个域名

还可以通过 View -> Focused Hosts 来自定义设置

### Sequence

网络请求按访问时间排序

可以设置是否 Auto scroll

若勾选 Focused，则只显示 Focus 的请求

Filter 可过滤 Code、Method、Host、Path、Status 等等

还可以设置 Filter uses regex

### Find in Session

Edit -> Find 或者 Command + F 调起 Find 窗口

![20241116122410](https://image.zuoright.com/20241116122410.png)

## Proxy

### Recording Settings

- Include
- Exclude

### External Proxy 外部代理

在一些复杂的网络结构中，可能需要经过多重代理才能访问某些服务或资源，比如要抓包的网站，本身就需要代理才能访问，则可以使用 Charles 代理后将流量转发到另一个代理

![20240706143357](https://image.zuoright.com/20240706143357.png)

Socket 或 HTTP 都可以，根据自己的代理进行选择，若选了 HTTP 通常还需要勾选上 HTTPS，如果不清楚就都配置上

![20241220224326](https://image.zuoright.com/20241220224326.png)

### Breakpoints 断点

打断点，修改请求或返回内容

### Throttle

模拟弱网

## Tools

### Map Remote

请求重定向，在不修改本地代码的情况下重定向网络流量到测试或开发环境

比如前端改动了一些东西，在发布之前重定向到线上，用真实数据填充查看下效果

在请求上，右键，选择 「Map Remote」

### Map Local

将网络请求重定向或者说映射到本地，模拟返回内容

在请求上，右键，选择 「Map Local」，然后选择返回的 Json 文件

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
