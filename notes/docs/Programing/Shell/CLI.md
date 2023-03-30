# CLI

- 内部命令
- 外部命令：可执行文件，可以被 whereis xxx 找到

## 命令行提示符

```shell
# 用户名@主机名:所在目录$/# 命令/可执行文件 选项 参数
root@localhost:~$ command [--options] parameter1 parameter2
<<"COMMENT"
系统管理员和普通用户的提示字符分别为：`#` 和 `$`  
选项名前通常用`-` 和 `--`表示简写和全写，也会有带+的情况  
指令间用至少一个空格来分隔，大小写敏感  
回车执行命令，`\`可将回车转义为换行输入  
执行多条命令，用分号隔开
COMMENT
```

修改主机名

```shell
sudo vim /etc/hostname
reboot  # 重启后生效
```

## 快捷键

- `Tab` 命令及选项补全/提示
- `Ctrl`+`c` 中断目前程序
- `Ctrl`+`d` 键盘输入结束EOF(End Of Input)
- `Shift`+`Page Up/Down` 向前/后翻看输出信息，`clear`后则翻不出来
