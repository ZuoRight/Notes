# VSCode

使用Vscode自带Python解析器导入包时`.vscode/settings.json`需要配置如下（[官方说明](https://github.com/microsoft/python-language-server/blob/master/TROUBLESHOOTING.md#unresolved-import-warnings)）

- Mac

```json
{
    "python.pythonPath": "env/bin/python",
    "python.autoComplete.extraPaths": ["./项目根目录"]
}
```

- Windows

```json
{
    "python.pythonPath": "env\\Scripts\\python.exe",
    "python.autoComplete.extraPaths": ["./项目根目录"]
}
```

如果还显示未导入，检查左下角解释器是否选对了。

### Settings Sync

- 上传：++shift+opt+u++（Windows用++alt++替代++opt++）
- 下载：++shift+opt+d++

首次上传/下载，会弹出配置，按提示填入token和gist

### PicGo

> <https://picgo.github.io/PicGo-Core-Doc/zh/guide/>

![20210808174641](http://image.zuoright.com/20210808174641.png)

- 从剪切板上传：++cmd+opt+u++（Windows用++alt++替代++opt++）
- 从文件夹上传：++cmd+opt+e++
- 输入路径上传：++cmd+opt+o++