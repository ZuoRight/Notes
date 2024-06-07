# VSCode

Visual Studio Code 只是一个编辑器，而不是 IDE

> IDE（Integrated Development Environment）`集成开发环境 = 编辑器 + 编译器 + 调试器`

VSCode 的一些配置等多端同步，现在已经不需要再使用 `Settings Sync` 扩展来管理，只需要登陆账号即可自动同步。

## 快捷键

切换终端：`Control` + `~`

## Python 配置

假设项目结构如下，在 `script.py` 中 `from modules import mod`，则 `PYTHONPATH` 需要设置为 `~/project/`，否则会报错：`ModuleNotFoundError: No module named 'xxx'`

```text
~/project/
    |
    |---modules/
        |
        |---mod.py
    |---scripts/
        |---script.py
```

在 PyCharm 中，选择源文件夹会自动设置自动执行的操作来完成的。但在 VSCode 中，需要手动设置。

参考：<https://stackoverflow.com/questions/53653083/how-to-correctly-set-pythonpath-for-visual-studio-code>

如果是在命令行中直接执行脚本，可以在代码中将项目路径加到 sys.path，也可以添加 `.vscode/settings.json` 配置，如果是调试则还需要添加 `.vscode/launch.json` 配置

### 代码中临时设置

可以在文件开头加上如下代码，但如果每个文件都这样设置则比较麻烦

```python
project_root = '/Users/name/demo'
if project_root not in sys.path:
    sys.path.insert(0, project_root)
```

### `settings.json`

修改后需要重新加载项目

```json
{
    "terminal.integrated.env.osx": {"PYTHONPATH": "${workspaceFolder}"},
    "terminal.integrated.env.linux": {"PYTHONPATH": "${workspaceFolder}"},
    "terminal.integrated.env.windows": {"PYTHONPATH": "${workspaceFolder}"}
}
```

### `launch.json`

如果没有配置 `settings.json` 也没有在代码中将 `project_root` 加到 `sys.path`，则需要添加 `cwd` 和 `env` 字段

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "cwd": "${fileDirname}",
            "env": {"PYTHONPATH": "${workspaceFolder}${pathSeparator}${env:PYTHONPATH}"}
        }
    ]
}
```

## PicGo

> <https://picgo.github.io/PicGo-Core-Doc/zh/guide/>

七牛云-密钥管理：<https://portal.qiniu.com/user/key>

![20210808174641](http://image.zuoright.com/20210808174641.png)

- 从剪切板上传：++cmd+opt+u++（Windows用++alt++替代++opt++）
- 从文件夹上传：++cmd+opt+e++
- 输入路径上传：++cmd+opt+o++
