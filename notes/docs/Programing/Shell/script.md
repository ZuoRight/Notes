# Script

<https://wangdoc.com/bash/script>

> 需要有x执行权限

指定解释器

- `#!/bin/bash`
- `#!/usr/bin/env bash`（推荐）

运行时会产生新的子进程：

- `bash ./filename.sh`（推荐，不用赋予执行权限）
- `./filename.sh`（必须声明，且要赋予执行权限）

运行时不会开启新进程，会影响当前shell，不用赋予执行权限：

- `source ./filename.sh`
- `. ./filename.sh`（.是source的简写）

## 管道 `｜`

进程通信的重要方式，把前一条命令的执行结果传给后一个命令，还会为两个命令创建子进程，由于内部命令在子进程中无法将结果传给父进程，所以不建议在管道中使用内部命令

比如常跟grep连用：`xxx | grep 参数 文件`
