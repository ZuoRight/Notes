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
- `\d`（digit） 任意数字，等同于`[0-9]`
- `\w`（word） 任意字母数字下划线（包括中文）

![20211025175108](http://image.zuoright.com/20211025175108.png)

> 注意：以上都是匹配的单个字符，如果要匹配多个，且往下看

- `{n}` n个
- `{m,n}` m～n个
- `{m,}` 最少m个
- `{,n}` 最多n个

> 除了用花括号表示，还可以简写为特定的符号

- `*` >=0，即任意多个，等同于`{0,}`，通配符`.*`表示任意多个任意字符
- `+` >=1，即至少1个，正数，等同于`{1,}`
- `?` 0或1个，即至多1个，有或没有，等同于`{0,1}`

---

- `aa|bb|cc` 多字符二选一，比如：`https?|ftp:\/\/`
- `[abcde]` 单字符多选一，取反：`[^abcde]`
- `[a-z]` 连续字母多选一
- `[1-10]` 连续数字多选一

---

括号`(正则)`，可把某部分看作一个整体，用于分组或者引用等，不保存子组`(?:正则)`，引用子组`\1`

## 边界

通常单词可以用`\w+`来表示，然后使用`\b`来限定单词的边界

![20211026182643](http://image.zuoright.com/20211026182643.png)

整段文本的边界

- `^x` 拖字符，必须以x开头
- `x$` 美元符，必须以x结尾

有些语言可能默认为多行模式，所以一种更严谨的方法是使用`\A`和`\z`（Python中是`\Z`）

如果是限定数字的边界，可以使用环视，具体参考[极客时间·专栏文章](https://time.geekbang.org/column/article/251972)

## 转义字符

常用于转义空白符，空格空格不需要转义，用普通的英文空格表示即可

- `\s` 任意空白符
- `\n` 换行，Windows平台的换行为`\r\n`
- `\r` 回车
- `\f` 换页
- `\t` 制表(水平)
- `\v` 制表(垂直)

反斜杠(`\`)在Python字符串中也表示转义，比如表示斜杠`\/`和反斜杠`\\`，也就是说如果要在Python中表示正则的`\d`则需要这样：`\\d`，很乱，所以在Python中建议使用r前缀`r"\d"`来取消字符串本身的转义

## 匹配机制

- 贪婪匹配 Greedy，尽可能多的匹配，回溯，默认
- 非贪婪匹配 Lazy，尽可能少的匹配，回溯，加`?`
- 独占模式 Possessive，不回溯的贪婪匹配，匹配不上即失败，加`+`

```python
import re

re.findall(r'a{1,3}', 'aaab')  # 贪婪模式，['aaa']
re.findall(r'a{1,3}?', 'aaab')  # 非贪婪模式，['a', 'a', 'a']
re.findall(r'a{1,3}+', 'aaab')  # 独占模式，内置的re库不支持会报错，re.error: multiple repeat
```

## 匹配模式

> 模式修饰符：

- `(?i)` 不区分大小写模式 Case-Insensitive
- `(?s)` 点号通配，改变的是`.`号的匹配行为，可以匹配换行，也叫单行模式 Single Line
- `(?m)` 多行匹配模式，改变的是`^`和`$`的匹配行为，可以匹配上每行的开头或结尾 Multiline
- `(?#comment)` 注释模式，可以在正则中添加注释

## 流派

### PCRE流派

支持以下字符组，比如各种编程语言

### POSIX流派

> 规范说明：<https://pubs.opengroup.org/onlinepubs/7990989775/xbd/re.html>

- GNU BRE(基础正则)，需要转义（`(), {}, +, ?, |`），比如：`grep`、`sed`
- GNU ERE(扩展正则)，无需转义，比如：`awk`，`egrep`

> `sed -r` 和 `grep -E`（其实就是`egrep`）也可以使用扩展正则

## Python中的正则表达式

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
匹配多个模式可用竖线分割：模式1 | 模式2

【最常用】
re.I：IgnoreCase，忽略大小写
re.S：Dotall， 点号通配模式 "."可以匹配任意字符，包括换行符

【不常用】
re.M：Multiline，多行模式
re.L：Locale，使预定字符类 \w \W \b \B \s \S 取决于当前区域设定
re.U：Unicode，使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性
re.X：Verbose，详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释
"""

text = '''we found “the little cat” is in the hat, we like “the little cat”'''
# 提取所有单词，所有单词都是用引号隔开，且中文引号内视作一个单词
regex = r'''\w+|“[^”]+“'''
re.findall(regex, text)
# ['we', 'found', '“the little cat”', 'is', 'in', 'the', 'hat', 'we', 'like', '“the little cat”']

# 匹配并替换
r = re.sub(regex, replace_regex, str, count=0, flags=0)
"""
replace_regex还可以是函数
count默认为0，表示替换全部
"""

test_str = "the little cat cat is in the hat hat hat, we like it."
# 去掉连续出现多次的重复单词
regex = r"(\w+)(\s\1)+"
subst = r"\1"
re.sub(regex, subst, test_str)  # "the little cat is in the hat, we like it."

# 正则表达式中可以用()分组，然后用group()提取分组
r.group(0)  # 返回原始字符串
r.group(n)  # 第n个分组
r.group(1,3,5)  # 返回多个分组
r.groups()  # 元组形式返回所有分组
r.span()  # 返回匹配的index

# 切分
re.split(r"pattern", "string")  # 返回一个字典
```

## 参考

> 在线正则验证网站：<https://regex101.com/>

1. [极客时间专栏《正则表达式入门课》](https://time.geekbang.org/column/article/259437)
2. <https://iswbm.com/58.html#11>
