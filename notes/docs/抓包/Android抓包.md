# Android 抓包

Android7.0+（`targetSdkVersion>=24`）系统不再信任用户证书，所以使用Charles等常用工具则无法再正常抓取到https接口，应对方式有以下4种

> <https://juejin.im/post/6844903831579394055>

- Root，将用户证书安装到系统证书目录

从抓包工具导出证书（以`.0`系统证书格式），用MT管理器将证书复制或者通过`adb remount`到`/etc/security/cacerts/`目录下（不要在sdcard中找这个目录直接拖拽过去）

- 修改配置文件 AndroidManifest

AndroidManifest中配置networkSecurityConfig

> <https://developer.android.com/training/articles/security-config>

- 调低targetSdkVersion<24

GooglePlay限制targetSdkVersion必须>=28，国内应用市场也开始逐步响这种限制。绝大多数App的targetSdkVersion都将大于24了，也就意味着抓HTTPS的包越来越难操作了。

- 借助targetSdkVersion<24的宿主

VirtualApp 或 平行空间（v4.0.8625以下版本，HttpCanary设置中可以直接安装）

## 平行空间

HttpCanary+平行空间

> v2.8.0前导出的证书名称不对，尽量用v2.8.0之后的版本

## [VirtualApp](https://github.com/asLody/VirtualApp/releases)

VirtualApp+HttpCarry

VirtualApp+[VirtualXposed](https://github.com/android-hacker/VirtualXposed)

> 参考：<http://jackzhang.info/2018/04/09/VirtualXposed/>
