# 正则表达式

正则表达式，Regular Expression，简称RE。

是一种描述文本内容组成规律的表示方式，常用于：

- 校验数据的有效性，比如校验手机号或邮箱格式是否正确
- 查找符合要求的文本内容，比如查找文本中所有的数字
- 对文本进行切割替换等操作，比如将手机号中间四位替换为星号

学会之后一定要克制：

- 最好要写注释，于人于己都是好事
- 能用普通字符串处理的，坚决用普通字符串处理
- 尽量将一个复杂的正则，分解为多个简单的正则

## 元字符

- `.` 除换行符外的任意字符（re.S模式下可以匹配换行符）
- `\s`（space） 任意空白符
- `\d`（digital） 任意数字
- `\w`（word） 任意字母数字下划线（包括中文）

![20211025175108](http://image.zuoright.com/20211025175108.png)

> 注意：以上都是匹配的单个字符，如果要匹配多个，且往下看

- `*` >=0，即任意多个
- `+` >=1，即至少1个
- `?` 0或1个，即至多1个，比如：colou?r
- `{n}` n个
- `{m,n}` m～n个
- `{m,}` 最少m个
- `{,n}` 最多n个

> 正则表达式默认为贪婪匹配，即尽可能多的匹配
>
> 加上`?`变为非贪婪匹配，即尽量少的匹配，比如`x{m,n}?`只匹配m个x

## 通配符

- `.*`
- `^` 拖字符，匹配开头
- `$` dollar符，匹配结尾

## 扩展元字符

- [] 中括号，匹配中括号中的任意一个字符，比如用来匹配大小写

[a-c] a或b或c
[abc] a或b或c
[^abc] 除了a或b或c

- a|b 匹配a或b

## 转义字符

- `\\` 反斜杠

以下都是空白符，空格就用普通的英文空格表示即可

- `\s` 任意空白符
- `\n` 换行
- `\r` 回车
- `\f` 换页
- `\t` 制表(水平)
- `\v` 制表(垂直)

## 流派

### POSIX流派

> 规范说明：<https://pubs.opengroup.org/onlinepubs/7990989775/xbd/re.html>

- GNU BRE(基础正则)，需要转义（`(), {}, +, ?, |`），比如：`grep`、`sed`
- GNU ERE(扩展正则)，无需转义，比如：`awk`，`egrep`

> `sed -r` 和 `grep -E`（其实就是`egrep`）也可以使用扩展正则

### PCRE流派

支持以下字符组，比如各种编程语言



## Python中的正则表达式

Python的字符串本身也是用\转义，为避免与正则表达式的转义混淆，所以强烈建议使用`r"xxx"`形式，否则将需要用转义字符表示反斜杠：`"\\\\"`

示例：`r"[\s\,\;]+"` 空格/逗号/分号

```python
import re

# 匹配字符串
# 匹配则返回列表
r = re.findall(r"pattern", "string", flags=0)
# 匹配则返回Match对象，不匹配返回None
r = re.match(r"pattern", "string", flags=0)
# 搜索字符串，以对象形式返回第一个匹配成功的结果
r = re.search(r"pattern", "string",flags=0)

"""
flags 匹配模式
匹配多个模式可用竖线分割：模式1 | 模式2 | 模式n...

【最常用】
re.I：IgnoreCase，忽略大小写
re.S：Dotall， "."可以匹配任意字符，包括换行符

【不常用】
re.M：Multiline，多行模式
re.L：Locale，使预定字符类 \w \W \b \B \s \S 取决于当前区域设定
re.U：Unicode，使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性
re.X：Verbose，详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释
"""

# 正则表达式中可以用()分组，然后用group()提取分组
r.group(0)  # 返回原始字符串
r.group(n)  # 第n个分组
r.group(1,3,5)  # 返回多个分组
r.groups()  # 元组形式返回所有分组
r.span()  # 返回匹配的index

# 匹配并替换
r = re.sub(r"pattern", "replace_string", "string", count=0, flags=0)

# 切分
re.split(r"pattern", "string")  # 返回一个字典
```

## 参考

> 在线正则验证网站：<https://regex101.com/>

1. [极客时间专栏《正则表达式入门课》](https://time.geekbang.org/column/article/259437)
2. <https://iswbm.com/58.html#11>
