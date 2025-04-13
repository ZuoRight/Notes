# Android 抓包

## Android 7.0（API 24） 的限制

防止中间人攻击，强制应用使用系统信任的证书链，用户 CA 证书默认不再被信任，从 SDCard 安装用户级证书将无法拦截应用流量，Google Play 以及国内应用市场逐步响应这种限制

`AndroidManifest.xml` 中引用的 `network_security_config.xml` 文件可配置证书信任策略

> <https://developer.android.com/training/articles/security-config>

```xml
<manifest ...>
    <application
        android:networkSecurityConfig="@xml/network_security_config"
        ...>
        <!-- 其他组件 -->
    </application>
</manifest>
```

```xml
<trust-anchors>
    <certificates src="system" />  
</trust-anchors>

<network-security-config>
    <base-config>
        <trust-anchors>
            <certificates src="system" />  <!-- 默认仅信任系统 CA -->
            <certificates src="user" />  <!-- debug 包可以让开发设置允许用户安装的 CA 证书以此方便测试 -->
        </trust-anchors>
    </base-config>

    <!-- 针对特定域名启用证书固定 -->
    <domain-config>
        <domain includeSubdomains="true">example.com</domain>
        <pin-set>
            <pin digest="SHA-256">AAAAAAAAAAAAAAAAAAAAAAAA=</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

如果为非 debug 包可以通过以下方案绕过

- Root，将抓包工具的 CA 证书安装到系统证书目录

比如从抓包工具导出证书（以 `<hash>.0` 系统证书格式），用 MT 管理器将证书复制或者通过 `adb remount` 到系统根证书目录下（`/system/etc/security/cacerts/`）

- 借助 `targetSdkVersion<24` 的应用作为宿主容器

比如 HttpCanary + 平行空间 / VirtualApp，或者 VirtualApp + VirtualXposed

## Android 14（API 34）的限制

应对日益复杂的网络攻击（如供应链攻击），限制非授权证书的滥用

Google 强化了证书固定（Certificate Pinning），用户 CA 证书作用域受限，部分关键服务（如 Google Play 服务）完全屏蔽用户证书，仅信任 Google 内置的 CA。开发者可通过声明式配置（如 AndroidManifest.xml）或代码级固定（如 OkHttp 的 CertificatePinner）

限制调试和 Hook 工具，增强对 Xposed、Frida 等动态注入工具的检测，导致 JustTrustMe 等模块失效，对非官方签名应用（如修改后的 APK）限制网络权限

解决方法

- 自定义 ROM：刷入修改版系统，禁用证书验证逻辑
- Root 并手动导入 CA 证书到系统目录
- 使用 MagiskTrustUserCerts 模块强制系统信任用户证书

## 平行空间

> HttpCanary 设置中可以直接安装 v4.0.8625 以下版本  
> v2.8.0 前导出的证书名称不对，尽量用 v2.8.0 之后的版本

HttpCanary 使用指南及抓包解决方案，<https://juejin.cn/post/6844903745562607624>，打断点：静态注入器

对 HTTP2 协议的抓包和注入，<https://juejin.cn/post/6844903760867639310>

## VirtualApp

<https://github.com/asLody/VirtualApp/releases>

- VirtualApp + HttpCarry
- VirtualApp + [VirtualXposed](https://github.com/android-hacker/VirtualXposed)，参考：<http://jackzhang.info/2018/04/09/VirtualXposed/>

## Xposed

是一款强大的 Android 框架，允许用户在不修改 APK 文件的情况下，动态修改系统和应用的行为。它通过钩子（Hook）技术拦截并改变 Java 方法调用，在 Android 的 Zygote 进程（所有 App 的父进程）中注入代码，劫持目标方法并替换逻辑，可安装一些第三方模块，比如 JustTrustMe.apk, JustMePlush.apk 等，实现各种高级定制功能（如去广告、破解会员、修改 UI 等）

劣质模块可能导致系统崩溃或耗电增加，恶意模块可能窃取密码、银行卡信息，所以支付宝微信等支付金融，以及游戏 App 会检测并封禁 Xposed

仅支持 Android 5.0~8.1，需刷入系统分区，因此需要解锁 Bootloader 并 Root，影响 OTA 更新

## Magisk

一种非常流行的工具，主要用于对安卓设备进行 Root，不仅能帮助用户获取超级用户权限，还有一些独特的功能

与传统 Root 方法不同，Magisk 通过 Systemless 方式实现 Root，也就是说它不会直接修改系统分区（`/system`），使得它更隐蔽，也更容易撤销。

Magisk 能够隐藏 Root 状态，让一些对 Root 敏感的应用（如银行应用、Google Pay、某些游戏）正常运行，这是通过 Magisk Hide 功能实现的。

Magisk 支持安装一些模块添加功能（比如广告屏蔽、系统优化等），而无需直接改动系统文件。

1. 解锁设备的 Bootloader
2. 刷入自定义 Recovery（如 TWRP）
3. 通过 Recovery 刷入 Magisk 的 ZIP 文件
4. 重启设备后，安装 Magisk Manager（现更名为 Magisk App）来管理 Root 权限和模块

### LSPosed

<https://github.com/LSPosed/LSPosed>

基于 Zygisk（Magisk 的注入技术），支持 Android 8~14，更轻量、更隐蔽，可针对单应用启用模块

- EdXposed 是 LSPosed 的前身，兼容性较差，已停止维护
- LSPatch 是免 Root 方案，通过重新打包 APK 的方式植入 Xposed 模块

### MagiskTrustUserCerts

<https://github.com/NVISOsecurity/MagiskTrustUserCerts>

一个 Magisk/KernelSU 模块，可自动将用户证书添加到系统根 CA 存储区

## 突破 SSL Pinning

SSL Pinning 是一种安全机制，用于防止中间人攻击（MITM），确保客户端只信任特定的服务器证书或公钥，而不是所有由系统信任的 CA 签发的证书

内置证书或者公钥的时候，需要对比验证客户端和服务端的证书或公钥，一般是通用的 API，所以可以直接控制这个 API 的返回结果让验证通过。

通过 hook 技术绕过

## 双向校验

通常客户端验证服务端的证书通过后就已经建立了安全通信，然后用账号、密码等手段就能够确认用户的真实身份。

但为了防止账号、密码被盗，还会采取双向证书校验，即服务端也要校验客户端的证书，这样会更加安全，比如网上银行使用 U 盾给用户颁发客户端证书，由于服务器端需要维护所有客户端的证书，大多数情况下一般不会双向校验。

Frida + Magisk + Lsposed

客户端的证书一定会存在本地代码中，逆向客户端 App，找到证书和密钥，转为 pkcs12 格式导入到 burp
