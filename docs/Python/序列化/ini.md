# INI

`*.ini` 初始化文件(Initialization file)

后缀名也可以是`.cfg`、`.conf`、`.txt` 等

```ini
[section]
; 注释
name=value
```

## 读取 ini

> Windows 不要使用中文注释

```python
import configparser

# 创建管理对象
conf = configparser.ConfigParser()

# 读ini文件
conf.read(conf_path, encoding="utf-8")

# 获取所有的section, 返回list
sections = conf.sections()
# 获取某个section下所有(key:value)，每一对用一个元组表示，返回一个元组list
items = conf.items('edit_page')
menu = conf.get('edit_page','menu')

print(sections)
print(items)
print(items[0])
print(items[0][1])
print(menu)
```
