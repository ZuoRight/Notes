# Airtest Project

由网易游戏团队开发，官方文档：<https://airtest.doc.io.netease.com/>

参考视频：<https://www.bilibili.com/video/BV1Fs4y187X8>

## Airtest IDE

是一款跨平台的 UI 自动化测试编辑器，内置了 Airtest 和 Poco 的相关插件功能，能够使用它快速简单地编写 Airtest 和 Poco 代码。

连接手机

- ADB 连接
- Javacap 连接（黑屏时）

## Airtest

```shell
pip install -U airtest

airtest run demo.air
```

`.air` 项目就是一个包含 `.py` 脚本和截图的文件夹

```python
from airtest.core.api import *

auto_setup(__file__)
connect_device('Android://emulator-5037')

touch(
    wait(
        Template(
            r"screenshot_xxx.png",
            record_pos=(0.234, 0,789),
            resolution=(936, 1769)
        )
    )
)
```

比如要控制手机自动进入设置页面进行一些操作，先截取「设置」应用的图像，即目标元素

然后 Airtest 会先截取整个手机屏幕的图像，根据 OenCV 算法识别目标元素

![20240720182751](https://image.zuoright.com/20240720182751.png)

大概原理是，将目标元素分割为 3*3 的 9 个点，中间的点序号就是 5，即 target_pos

去跟屏幕截图对比，匹配度达到 70%（可设置），即 threshold 0.7，则认为定位成功，将进行后续操作

勾选 rgb 表示用彩色像素匹配
