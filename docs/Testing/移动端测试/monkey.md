# Monkey

<https://developer.android.com/studio/test/other-testing-tools/monkey>

存在于手机中，可用 ADB 发送伪随机用户事件控制 Monkey

> 高频模拟用户事件，对手机的损耗较大，请谨慎使用

```shell
adb shell monkey [options] event-count > /path/log.txt
# options
'
-v 指定打印信息的详细级别
-p 指定包名，可指定多个包名(每个都要加-p)
-c 指定类别，可指定多个(每个都要加-c)
-s 伪随机数生成器的seed值，如果seed相同，则两次monkey 测试产生的事件序列也相同

--throttle 500 两次随机事件间间隔500ms
--pct-xxx <percent> 调整各种事件的比例
忽略异常
    --ignore-crashes 忽略Crash
    --ignore-timeouts 忽略ANR
    --ignore-security-exceptions 忽略权限提示
    --ignore-native-crashes 忽略native层crashes
--kill-process-after-error 发生错误后直接杀掉进程
--hprof 在事件序列发送前后会立即生成分析报告，建议指定
--monitor-native-crashes 跟踪本地方法的崩溃问题
--dbg-no-events 初始化启动的activity，但是不产生任何事件
--wait-dbg 直到连接了调试器才执行monkey测试
'
```

质量要求：版本 release（发布）前，Monkey 跑出来的结果中 Crash 要为 0，最终发布前，Monkey 跑完的总次数应为 25W，结果里不允许有 nullPointException 出现

![20240720122230](https://image.zuoright.com/20240720122230.png)

## MonkeyRunner

提供了一系列的 API，可以完成模拟事件及截图操作，多设备控制，功能测试，回归测试等

`monkeyrunner demo.py` 如果出现 `'..\framework\x86_64' does not exist 错误，需要修改 monkeyrunner.bat`

```python
from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage

# MonkeyRunner 用来连接设备或模拟器
MonkeyRunner.alert(string message, string title, string okTitle)  # 警告框
device = MonkeyRunner.waitForConnection(float timeout, string deviceid)  # 等待设备连接，有多个 device id，需要指明具体哪个设备
MonkeyRunner.sleep(3)  # 等待3s


# MonkeyDevice 提供安装/卸载应用，发送模拟事件
device.startActivity(package/activity)  # 启动应用
device.type(string message)  # 输入
device.drag(tuple start, tuple end, float duration, integer steps)  # 拖动（起点位置，终点位置，持续时间，插值点的步数，默认10）
device.touch(integer x, integer y, integer type)  # 点击
device.press(string keycode, dictionary type)  # 按键
'''
KEYCODE_ENTER 回车键
Down
UP
DOWN_AND_UP
'''
image = device.takeSnapshot( )  # 截屏

# MonkeyImage 完成图像保存，及时对比操作
boolean sameAs(MonkeyImage other, float percent)  # 图像对比
image.writeToFile('./test.png', 'png')  # 保存图像文件（存储路径，存储类型png等）
```

## MonkeyScript

一组可以被 Monkey 识别的命令集合，可以完成重复固定的操作，不受分辨率影响，可移植性较 Monkeyrunner 要好

```shell
adb push monkey.py /data/local/tmp/
adb shell monkey.py -f  1000
```

`monkey.py`

```python
# 开头照抄即可
typ=user
count=10
speed=1.0
start data >>


UserWait(1000)  # 等待事件

LaunchActivity(package, package.Activity)  # 启动应用活动
'''
package：包名
package.Activity：包名.活动名（页面名称）
'''

DispatchPress(int keycode)  # 按下键值，Home：3，返回：4

DispatchPointer(10, 10, int action, float x, float y, 1, 1, -1, 1, 1, 0, 0)  # 点击事件
DispatchTrackball(10, 10, int action, float x, float y, 1, 1, -1, 1, 1, 0, 0)  # 轨迹球事件
'''
int action
    0：代表按下
    1：代表弹起
x和y代表坐标点
其他值都默认即可
'''

DispatchString(String text)  # 输入字符串事件
```
