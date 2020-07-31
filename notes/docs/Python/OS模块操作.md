# OS模块

os模块封装了操作系统的目录和文件操作  
注意：有些在os模块中，有些在os.path模块中  
另外：**shutil** 模块是对os模块的一个补充

`import os`

## os.xxx

```python
os.name  # 操作系统类型（posix代表Linux/Unix/Mac OS X，nt代表Windows）
os.uname()  # 获取详细操作系统信息（Windows不支持）
```

```python
os.environ  # 环境变量
os.environ.get('PATH')  # 获取PATH变量的值
```

```python
os.mkdir()  # 创建目录
os.rmdir()  # 删除目录

os.rename()  # 重命名文件
os.remove()  # 移除文件
```

## os.path.xxx

```python
os.path.abspath('.')  # 查看当前目录的绝对路径
os.path.abspath('xxx')    # 获取xxx的绝对路径

os.path.splitext('/path/to/file.txt')  # ('/path/to/file', '.txt')

```

## os.system()

```python
os.system("bash command")  # 运行shell命令
```
