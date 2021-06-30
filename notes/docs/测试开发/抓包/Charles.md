# Charles

对于工具的使用，最好的学习方式就是看[官方文档](https://www.charlesproxy.com/documentation/tools/)

- Structure 网络请求按访问域名分类
- Sequence 网络请求按访问时间排序

## 抓取HTTP包

### 配置代理

Tips：端口号随意，一般设为8888

![20200802230557](http://image.zuoright.com/20200802230557.png)

### 代理Web

Tips：每次重启Charles都需要重新勾选一下

![20200803232853](http://image.zuoright.com/20200803232853.png)

### 代理APP

Tips：

1. 需要将手机和电脑保持在同一局域网内，然后手动配置下相应网络的HTTP代理
2. 保存后，Charles界面会弹出确认框，点击允许（若没弹框可以重启下Charles试试）

![lALPDhYBNT2oVhrNA1LNBsY_1734_850](http://image.zuoright.com/lALPDhYBNT2oVhrNA1LNBsY_1734_850.png)

![lADPDhJztpjkh8HNBTbNAu4_750_1334](http://image.zuoright.com/lADPDhJztpjkh8HNBTbNAu4_750_1334.jpg)

## 抓取HTTPS包

1. 配置SSL代理，端口号为443
2. 根据不同操作系统安装相应证书

![20200802230833](http://image.zuoright.com/20200802230833.png)

### Windows安装证书

![20200802224500](http://image.zuoright.com/20200802224500.png)

![20200802224002](http://image.zuoright.com/20200802224002.png)

### Mac安装证书

![lALPDgfLOqpB3xzNAvLNCFg_2136_754](http://image.zuoright.com/lALPDgfLOqpB3xzNAvLNCFg_2136_754.png)

![lALPDgfLOqvqYXzNBgTNCC4_2094_1540](http://image.zuoright.com/lALPDgfLOqvqYXzNBgTNCC4_2094_1540.png)

### iOS安装证书

1. 手机网络配置代理后，浏览器访问`chls.pro/ssl`，下载证书
2. 打开设置 > 通用 > 描述文件 > 点击Charles证书 > 安装
3. 打开设置 > 关于本机 > 证书信任设置 > 找到Charles证书 > 启用信任

![lADPDgQ9vAXnSl_NBQDNAtA_720_1280](http://image.zuoright.com/lADPDgQ9vAXnSl_NBQDNAtA_720_1280.jpg)

### Android

## 功能

- focus 聚焦显示想看的域名，其它域名归类到其它
