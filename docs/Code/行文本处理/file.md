# 文件管理

Linux 中一切皆文件，也可以说一切皆字节流，或者说一切皆文件描述符

## 目录文件

目录树

![4d8207054d6d55daab264f050e11515](https://image.zuoright.com/4d8207054d6d55daab264f050e11515.jpg)

目录主要记录文件名列表，类似于抽屉，不同的目录依据FHS标准有不同的用途

![20210821215536](https://image.zuoright.com/20210821215536.png)

```shell
/  # 根目录

/root  # root用户的家目录
/home/username  # 普通用户家目录

/usr  # Unix Software Resource 主要存放系统资源，而不是用户的资源
/usr/bin  # 系统预装命令
/usr/sbin  # 系统预装管理命令
/usr/local  # 类似Windows的C:/Progrem Files，存放一些自编译的软件

/bin  # 包括/usr/bin的系统命令，以及普通用户命令
/sbin  # 包括/usr/sbin

/opt  # 类似Windows的D:/Software ，存放一些第三方软件

/etc  # 配置文件目录
/etc/opt  # 存放第三方软件的配置

/var  # 存放一些缓存日志等文件
```

### 切换目录

```shell
# print working directory 显示目录名称(绝对路径)
pwd [-P]  # -P 显示link文件的真实目录

basename /etc/path/file  # 返回文件名：file
dirname /etc/path/file  # 返回目录名：/etc/path

# change directory 切换目录，不带参数则默认回到家目录
cd [dirname]
"""
.  当前目录
.. 上层目录
~  家目录
-  前一个目录
"""
```

### 查看目录

```shell
# 树形结构显示目录
tree path [-L 2]  # -L 2 只显示到二级目录

# 查看目录，可同时查看多个目录
ls [-a] path1 path2
'
默认只显示当前目录一级非隐藏文件
  -a 可查看隐藏文件
  -R 递归显示子目录文件

默认显示短格式，-l 长格式显示，可查看文件权限等详情
  -l 文件大小用M为单位显示
  -t 按照时间顺序显示
  -r 时间逆序显示
'
```

![20210821163302](https://image.zuoright.com/20210821163302.png)

```shell
# -d 查看指定文件的长格式
ls -ld /test
'
total 文件总数
- 文件类型，通常目录显示为蓝色，文件为白色
  - 普通文件：纯文本文件(ASCII，源代码)、二进制文件(binary，图片视频等)、数据文件(data)
  d 目录文件
  l 符合链接：类似Windows的快捷方式
  b 块特殊文件，设备与设备文件（通常集中存放在`/dev/...`，比如硬盘与软盘等区块文件(`/dev/sda`)
  c 字符特殊文件，字符终端
  s 套接字文件
  f 命名管道，FIFO(先进先出)
- 不同身份的权限：文件属主 文件属组 其他用户
- 有多少文件名链接到此节点
- 所属用户
- 所属用户组
- 文件容量大小（Bytes）
- 最后修改时间
  mtime Modify Time 修改时间，只要文件改动就会变，比如权限大小等
  atime Access Time 读取时间
  btime Birth  Time 创建时间
  ctime Change Time 只有文件内容更改才会变
- 文件名
'
```

### 创建/删除目录

```shell
# make directory 创建目录，可同时创建多个
mkdir dir1 dir2
"""
-p 创建多级目录：mkdir dir/sub_dir
-m 设置权限：mkdir -m 711 dir

目录如果已存在则创建失败，加-p则会跳过
"""

# remove_directory 删除目录，只能删除空目录
rmdir [-p] dir
"""
-p 递归删除
"""
# 删除非空目录
rm -r dir  # 删除时需要确认，加-f不需要确认提示
```

## 普通文件

### 查找文件

```shell
# 全局查找，很慢，默认直接从根目录开始搜索
find path -mtime -4  # 找出4天内被修改过的文件
'
-user 查找属于指定user的文件
-type 要查找的文件类型
-regex 使用正则匹配
'

# 利用/etc/lib/mlocate数据库记录模糊查询文件
# 只能通过文件名查找，但比find更快
locate filename

# 可以查找$PATH及系统路径中的各种文件
whereis filename

# 只能查找$PATH路径中的可执行文件
which [-a] command  # -a 找出所有
```

根据内容查找

```shell
# 查找所有使用了 requests 库的文件
rg -t py 'import requests'
# 查找所有没有写 shebang 的文件（包含隐藏文件）
rg -u --files-without-match "^#!"
```

### 查看文件

```shell
wc -l file  # 统计文件有多少行

head [-3] file  # 头，显示前3行，默认显示前10行
tail [-3] file  # 尾，显示后3行，默认显示后10行
tail -f file  # 持续显示文件内新增内容，直到ctrl-c退出
tail -fn 1000 test.log | grep "key"  # 查看日志文件常用命令

cat [-n] file  # concatenate 串联 从第一行开始，-n显示行号
tac file  # cat的反写，从最后一行开始

# 如果只想取一个文件的第7~10行，并且显示行号
# 可以先用显示行号，然后取出前10行，最后再取出后(10-6)行
cat -n file | head -10 | tail -4

# 如果内容过多可以过滤
more filename
less filename
cat filename | more/less  # 结合cat一起使用
```

### 操作文件

```shell
# 创建空文件（还可以用来改mtime）
touch filename

# 删除文件
rm file  # 如果要删除目录加-r

# 复制文件 copy
cp 源文件 目标文件
'
-r 如果要复制目录加-r
-v 显示复制详情
-i 如果与目标文件重复询问是否覆盖
-a 等同于-dpR 复制并保留文件所属用户、权限、时间等属性，以及递归复制子目录
'

# move 移动/重命名文件
mv 源文件 目标文件
```

## 链接

### 硬链接(hard link)

`ln file file_link`

指向文件的 inode，即与原文件共享 i 节点

类似副本但不占用空间，删除原文件不影响硬链接

不允许给目录创建硬链接

### 软链接(symbolic link)

`ln -s file file_link`

也叫符号链接，类似 Win 的快捷方式，只是原文件的文本指针

会占用空间，因为要存储路径信息，删除原文件，软链接将失效

## 权限操作

> root用户不受权限限制，无论什么权限都可读写，属主和属组权限冲突时以属主为主

### 普通权限

- 读(read) `r`  4
- 写(write) `w`  2
- 可执行(execute) `x`  1
- 没有任何权限 `-` 0 比如/etc/shadow

> 创建文件时默认权限根据umask值计算，属主和属组根据当前进程的用户来设定
>
> - 新建目录默认权限为：管理员创建的为755，普通用户创建的为775，都有可执行权限
> - 新建文件默认权限为，管理员创建的为644，普通用户创建的为664，都没有可执行权限

在Windows中文件是否可执行由后缀决定，在Linux中则由权限x决定

> 不过拥有可执行权限不代表真的可以执行，还是得看文件内容是否为可执行代码才行
>
> 另外扩展名不影响执行，但通常用来区分不同用途，比如`*.sh`、`*.tar`、`*.html`等

如果想rwx某个目录下某个文件，则必须要拥有这个目录的x权限才行，比如要删除目录内某文件，首先要有x权限进入目录，然后要有w权限删除文件（不管文件本身什么权限，文件的w权限只是可编辑写入文件的权限，并不能删除文件）

### 特殊权限

普通的rwx权限不足以满足日常需要，所以设置了一些特殊权限，了解即可，不建议手动修改

- SUID `s` 用于二进制文件，执行命令时取得文件属主权限，比如/usr/bin/passwd
- SGID `d` 用于目录，在某目录下创建的新文件或目录，其权限自动改为该目录属组
- SBIT `t` 用于目录，在某目录下创建的文件或目录，仅root和自己可以删除，比如/tmp/

### 修改权限

```shell
# 使用字符修改
chmod u=rwx,go=rx xxx
chmod a+x xxx
"""
u user 用户属主
g group 用户属组
o other 其他用户
a all 所有用户

= 设置权限
+ 增加权限
- 去掉权限
"""

# 使用数字修改
chmod 755 xxx
"""
目录被创建时默认权限为drwxr-xr-x，即755
  7=4+2+1 表示r+w+x权限全开
  5=4+1 表示r,x权限
"""
```

### 修改所属主和属组

```shell
chown user1 xxx  # 只修改属主
chown :group1 xxx  # 只修改属组
chown user1:group1 xxx  # 修改属主和属组

chgrp [-R] group1 xxx  # 单独更改属组（不常用）将目录的用户组改为group1，（-R 子目录一并修改）
```

## 解压缩

```shell
# 打包
tar cf <打包后的文件> <被打包的文件>
"""
c 打包
x 解包
f 指定操作类型为文件
"""
tar cf /tmp/etc-backup.tar /etc  # 打包配置文件目录
tar cfz /tmp/etc-backup.tar.gz /etc  # 打包并压缩成gzip格式，扩展名通常可简写为.tar.gz，还可以更简：.tgz
tar cfj /tmp/etc-backup.tar.bz2 /etc  # 打包并压缩成bzip2格式(压缩率更高，压缩速度较慢一些)，扩展名可简写为：.tbz2

# 解包
tar xf <被解压文件> -C <解压路径>
"""
-C 解压路径，比如：/usr/local/src
v 校验
"""
tar xf /tmp/etc-backup.tar -C /tmp
tar xfz /tmp/etc-backup.tar.gz -C /tmp
tar xfj /tmp/etc-backup.tar.bz2 -C /tmp
```
