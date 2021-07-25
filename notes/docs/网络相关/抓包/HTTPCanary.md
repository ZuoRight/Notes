# HttpCanary

使用指南及抓包解决方案
https://juejin.cn/post/6844903745562607624
打断点：静态注入器


对HTTP2协议的抓包和注入
https://juejin.cn/post/6844903760867639310


Android 7.0+系统（应用targetSdkVersion>= 24）不再信任用户CA证书
但GooglePlay开始限制targetSdkVersion必须>=26，国内应用市场也开始逐步响应这种限制

所以我们需要targetSdkVersion<24的应用做容器：
平行空间：低于4.0.8625的版本，HttpCanary中可以直接安装
VirtualApp


VirtualApp+httpcarry替换平行空间在手机上抓包
GitHub：https://github.com/asLody/VirtualApp/releases


VirtualApp+VirtualXposed
Github：https://github.com/android-hacker/VirtualXposed
http://jackzhang.info/2018/04/09/VirtualXposed/


自己的APP改配置文件：https://developer.android.com/training/articles/security-config
