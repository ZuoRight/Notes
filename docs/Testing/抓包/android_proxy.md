# Android 抓包

Android 7.0+（`targetSdkVersion>=24`）系统不再信任用户证书，从 SDCard 安装用户级证书将无法拦截应用流量，Google Play 以及国内应用市场逐步响应这种限制，所以使用 Charles 等常用工具则很难再正常抓取到 HTTPS 接口的请求内容，应对方式有以下几种

- Root，将用户证书安装到系统证书目录

> 需要 Root 权限

从抓包工具导出证书（以 `.0` 系统证书格式），用 MT 管理器将证书复制或者通过 `adb remount` 到系统根证书目录下（`/etc/security/cacerts/`）

- 修改配置文件 AndroidManifest

AndroidManifest 中配置 networkSecurityConfig

> <https://developer.android.com/training/articles/security-config>

- 调低 `targetSdkVersion<24`

GooglePlay 限制 `targetSdkVersion>=28`，国内应用市场也开始逐步响这种限制。绝大多数 App 的 targetSdkVersion 都将大于 24 了，也就意味着抓 HTTPS 的包越来越难操作了。

- 借助 `targetSdkVersion<24` 的应用作为宿主容器

比如 HttpCanary +  平行空间 / VirtualApp

或者 VirtualApp + VirtualXposed

## 平行空间

> HttpCanary 设置中可以直接安装 v4.0.8625 以下版本  
> v2.8.0 前导出的证书名称不对，尽量用 v2.8.0 之后的版本

HttpCanary 使用指南及抓包解决方案，<https://juejin.cn/post/6844903745562607624>，打断点：静态注入器

对 HTTP2 协议的抓包和注入，<https://juejin.cn/post/6844903760867639310>

## VirtualApp

<https://github.com/asLody/VirtualApp/releases>

- VirtualApp + HttpCarry
- VirtualApp + [VirtualXposed](https://github.com/android-hacker/VirtualXposed)，参考：<http://jackzhang.info/2018/04/09/VirtualXposed/>

## 逆向反编译

### SSL-Pinning

1. 客户端内置证书，即在客户端内置仅接受指定域名的根证书，而不接受操作系统或浏览器内置的CA根证书对应的任何证书
2. 客户端内置公钥

### 突破 SSL-Pinning

<https://zhuanlan.zhihu.com/p/60392573>

内置证书或者公钥的时候，需要对比验证客户端和服务端的证书或公钥，一般是通用的API，所以可以直接控制这个API的返回结果让验证通过。

1. Xposed + justTrustme：不行？
2. apk 反编译工具
3. 使用逆向神器 Frida：太难？

使用模拟器（首选逍遥模拟器），模拟器不行，使用真机，需要 root，root 工具（首选刷机精灵）
