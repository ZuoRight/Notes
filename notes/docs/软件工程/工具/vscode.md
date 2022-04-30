# VSCode

## 配置同步

VSCode的一些配置等多端同步，现在已经不需要再使用`Settings Sync`扩展来管理，只需要登陆账号即可自动同步。

## Python

使用VSCode自带的Python解析器，如果非根目录下引用了自定义包，在debug时会出现找不到包的情况，这貌似是一个已知但一直未解决的问题

一种解决方式是可以在文件开头加上如下代码，但这样比较麻烦

```python
import sys
import os
curPath = os.path.abspath(os.path.dirname(__file__))
rootPath = os.path.split(curPath)[0]
sys.path.append(rootPath)
```

另一种是根据提示在根目录下的`.vscode/launch.json`配置中指定`"env:{}"`，具体如下

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: 当前文件",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {
                "PYTHONPATH": "${workspaceFolder}"
            }
        }
    ]
}
```

## PicGo

> <https://picgo.github.io/PicGo-Core-Doc/zh/guide/>

![20210808174641](http://image.zuoright.com/20210808174641.png)

- 从剪切板上传：++cmd+opt+u++（Windows用++alt++替代++opt++）
- 从文件夹上传：++cmd+opt+e++
- 输入路径上传：++cmd+opt+o++
