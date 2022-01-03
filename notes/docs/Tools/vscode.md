# VSCode

> VSCode的一些设置等多端同步，现在已经不需要再使用`Settings Sync`扩展来管理，只需要登陆账号即可自动同步。

使用VSCode自带Python解析器导入包时`.vscode/settings.json`需要配置如下（[官方说明](https://github.com/microsoft/python-language-server/blob/master/TROUBLESHOOTING.md#unresolved-import-warnings)）

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

## PicGo

> <https://picgo.github.io/PicGo-Core-Doc/zh/guide/>

![20210808174641](http://image.zuoright.com/20210808174641.png)

- 从剪切板上传：++cmd+opt+u++（Windows用++alt++替代++opt++）
- 从文件夹上传：++cmd+opt+e++
- 输入路径上传：++cmd+opt+o++
