# VirtualBox

> 官网下载地址：<https://www.virtualbox.org/wiki/Downloads>
>
> 参考手册：<https://www.virtualbox.org/manual/>

- 下载，选择对应操作系统的安装包下载即可

> VirtualBox暂不支持arm64架构的M1等机型

![20220707002004](https://image.zuoright.com/20220707002004.png)

- 安装，一直下一步即可

![20220707001546](https://image.zuoright.com/20220707001546.png)

- 安装扩展（可选）

![20220707002443](https://image.zuoright.com/20220707002443.png)

## 安装 Ubuntu 系统

- 新建虚拟机

如果是为了练习k8s，CPU最低2核，建议虚拟机内存4G+，硬盘40G+，为了节约资源，声卡摄像头软驱等不必要的设备都可以禁用或卸载

![20220707003014](https://image.zuoright.com/20220707003014.png)

- 选择镜像

![20220707003124](https://image.zuoright.com/20220707003124.png)

- Try or Install

此处需要小等一会儿...

![20220707003257](https://image.zuoright.com/20220707003257.png)

- 选择中文

![20220707003427](https://image.zuoright.com/20220707003427.png)

- 键盘布局

从这里开始，界面可能就会显示不全，需要后面安装增强功能来解决

此时可以双击或者凭感觉用Tab键和回车键来选择继续

亦或者先退出，然后进入虚拟机设置-显示，显卡控制器临时改为VBoxSVGA再安装（安装完后切换回VMSVGA）

![20220707003506](https://image.zuoright.com/20220707003506.png)

- 最小安装（建议）

选最小安装并不会节省安装时间，只会在安装完后自动帮我们卸载掉多余的软件

取消勾选安装Ubuntu时下载更新是可以节省时间的，可以安装后再更新

![20220707003544](https://image.zuoright.com/20220707003544.png)

- 继续

![20220707003643](https://image.zuoright.com/20220707003643.png)

- 继续

![20220707003655](https://image.zuoright.com/20220707003655.png)

- 时区，选择 ShangHai

![20220707003723](https://image.zuoright.com/20220707003723.png)

- 创建用户，继续

![20220707003737](https://image.zuoright.com/20220707003737.png)

- 然后就开始漫长的安装过程

![20220707004037](https://image.zuoright.com/20220707004037.png)

- 弹出启动盘（默认已弹出），直接回车

![20220707004226](https://image.zuoright.com/20220707004226.png)

- 登陆

理论上会出现登录页面，登陆后或许会卡住，此时可以退出

切回VMSVGA显卡，可以把显存调大一些，并启用3D加速

![20220707010459](https://image.zuoright.com/20220707010459.png)

### 安装增强功能

> 官方下载链接：<http://download.virtualbox.org/virtualbox/6.1.4/>

- 下载 Guest Additions

![20220707004944](https://image.zuoright.com/20220707004944.png)

- 虚拟机设置-存储-选择镜像

![20220707005104](https://image.zuoright.com/20220707005104.png)

- 启动虚拟机后找到镜像

![20220707005126](https://image.zuoright.com/20220707005126.png)

- 执行`./autorun.sh`

安装完成后回车，重启，然后就可以设置分辨率（切换全屏：右Ctrl+F），共享粘贴板了

![20220707005428](https://image.zuoright.com/20220707005428.png)

### 安装更新

![20220707005219](https://image.zuoright.com/20220707005219.png)

### 设置网络

> 参考手册：<https://www.virtualbox.org/manual/ch06.html>

- 如果想让虚拟机与宿主机间网络互通，可以设置网卡为Host-Only模式
- 如果想让同一宿主机的虚拟机与虚拟机间网络互通，可以设置网卡为NAT模式
- 如果想让其它物理主机与虚拟机间网络互通，需要设置网卡为桥接模式，此模式可以实现各种场景通信

### 快照

配置好虚拟机后，最好创建一个快照，方便有问题时使用快照恢复到初始状态，而不用再重新安装。
