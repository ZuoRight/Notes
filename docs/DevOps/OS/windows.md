# Windows

Windows XP > Win7 > Win10

内核是 Windows NT

编译环境 Microsoft Visual C++, MSVC, 简称 VC++

## Win + R

- regedit 注册表
- msconfig 修改系统启动项
- calc 计算器
- notepad 记事本


## CMD

CMD 是从 DOS 系统继承下来的产物，而 PowerShell 是支持脚本语言的运行环境，可以使用管道符，底层通过别名的方式，兼容 CMD 和 一些常用的 Linux 命令，比如 `cd` 其实是 `Set-Location` 的别名，`ls` 是 `Get-ChildItem` 的别名

```shell
cls  # 清屏

dir  # 类似 ls，查看当前文件夹下的文件

d:  # 切换盘符
cd <file>  # 切换路径，切换有空格的路径需要引号括起
cd ..   # 切换到上一级

findstr  # 类似 grep

netstat -ano  # 查看端口号占用进程
netstat -aon | findstr "xxx"  # ps

ipconfig /all  # 查看 IP 和 DNS
ipconfig /flushdns  # 清理 DNS 缓存

# 使用变量需要先设置
set key=value
```

## Linux 子系统

- Cygwin

Cygwin 是针对 Win32 编译的 GNU 和开源工具的一个集合程序，模拟提供了类似于 Linux 的功能，可以运行 Bash 脚本，但不能运行 Apache、Docker 或其他真正的 ELF 二进制文件和 Linux 应用程序。

- WSL

Windows Subsystem for Linux，最初叫做 Bash on Ubuntu on Windows

运行在 Windows 10 上的完整本地 Linux，WSL2 附带一个真正的 Linux 内核并在 Windows 上运行。

可以原生运行 Linux 二进制可执行文件（ELF 格式），与 Windows 共享文件系统（磁盘挂载在 `/mnt`），在命令中加上 `.exe` 后缀就可以直接执行 `.exe` 程序
