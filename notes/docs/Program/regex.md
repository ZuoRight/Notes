# 正则表达式

> 在线验证：<https://regex101.com/>

RE（Regular Expression）是一种描述文本内容组成规律的表示方式，常用于：

- 校验数据的有效性，比如校验手机号或邮箱格式是否正确
- 查找符合要求的文本内容，比如查找文本中所有的数字
- 对文本进行切割替换等操作，比如将手机号中间四位替换为星号

学会之后一定要克制：

- 最好要写注释，于人于己都是好事
- 能用普通字符串处理的，坚决用普通字符串处理
- 尽量将一个复杂的正则，分解为多个简单的正则

## 规范

> 参考：<https://time.geekbang.org/column/article/254156>

现代编程语言绝大多数使用的正则表达式引擎都起源于 Perl 语言，为此形成了一套标准，`PCRE`(Perl Compatible Regular Expressions)，即 Perl 语言兼容正则表达式。

但在 Linux 系统中使用的正则，遵循的是更早的POSIX标准，不支持 `\d`、`\w`、`\s` 这类字符组简记方式

> `grep -P` 可以使用 PCRE 正则

另外根据使用 `()`、`{}`、`+`,、`?`、`|` 等符号是否需要转义又分为基础正则和扩展正则：

- 基础正则(GNU BRE)，需要转义，比如：`sed`、`grep`
- 扩展正则(GNU ERE)，无需转义，比如：`awk`、`egrep`、`sed -r`、`grep -E`

## 元字符

### 匹配单个

- `aa|bb|cc` 多字符二选一，比如：`https?|ftp:\/\/`
- `[abcde]` 单字符多选一
- `[a-z]` 连续字母多选一
- `[1-10]` 连续数字多选一
- `[0-9a-fA-F]` 可以匹配任意一个16进制的数字
- `[^abcde]` 取反

简记方式

- `.` 除换行符外的任意字符（re.S 模式下可以匹配换行符）
- `\s`（space）任意空白符
- `\d`（digit）任意数字，等同于：`[0-9]`
- `\w`（word）任意字母数字下划线（包括中文）

![20211025175108](http://image.zuoright.com/20211025175108.png)

### 匹配多个

- `{m}` m个
- `{m,n}` m～n个
- `{m,}` 最少m个
- `{,n}` 最多n个

简记方式

- `?` 等价于：`{0,1}` 至多1个，0个或1个
- `+` 等价于：`{1,}` 至少1个，大于等于1个
- `*` 等价于：`{0,}` 任意多个，大于等于0个

### 组合

- `.*` 任意多个任意字符
- `.+` 至少一个任意字符，匹配核心文本内容
- `\w+` 单词

```python
# 提取所有单词，所有单词都是用引号隔开，且中文引号内视作一个单词
text = '''we found “the little cat” is in the hat, we like “the little cat”'''
regex = r'''\w+|“[^”]+“'''
re.findall(regex, text)  # ['we', 'found', '“the little cat”', 'is', 'in', 'the', 'hat', 'we', 'like', '“the little cat”']


# 去掉连续出现多次的重复单词
test_str = "the little cat cat is in the hat hat hat, we like it."
regex = r"(\w+)(\s\1)+"
subst = r"\1"
re.sub(regex, subst, test_str)  # "the little cat is in the hat, we like it."
```

## 边界

### 限定单词的边界

- `\b`

![20211026182643](http://image.zuoright.com/20211026182643.png)

### 限定文本的边界

- `^`x 拖字符，必须以x开头
- x`$` 美元符，必须以x结尾

有些语言可能默认为多行模式，所以一种更严谨的方法是使用 `\A` 和 `\z`（Python中是 `\Z`）

### 限定数字的边界

可以使用环视，具体参考：[极客时间·专栏文章](https://time.geekbang.org/column/article/251972)

## 分组和引用

`()` 括号，可把某部分看作一个整体，用于分组或者引用等

- 不保存子组：`(?:正则)`
- 引用子组：`\1`

## 匹配模式

- `(?i)` Case-Insensitive 忽略大小写
- `(?s)` Single Line 单行模式，即 `.` 号可以匹配包括换行符在内的任意字符，在提取爬虫内容时常用
- `(?m)` Multiline， 多行匹配模式，`^` 和 `$`可以匹配上每行的开头或结尾
- `(?#comment)` 注释模式，可以在正则中添加注释

```python
import re

re.I  # Case-Insensitive
re.S  # Single Line
re.M  # Multiline

re.L  # Locale 本地化识别
re.A  # ASCII字符模式
re.U  # Unicode 根据Unicode字符集解析字符，影响 \w\W \b\B
re.X  # Verbose 忽略字符串中的空白、tab、换行符
```

- 贪婪匹配 Greedy，尽可能多的匹配，回溯，默认
- 懒惰模式 Lazy，尽可能少的匹配，回溯，加 `?`
- 独占模式 Possessive，不回溯的贪婪匹配，匹配不上即失败，加 `+`

```python
import re

re.findall(r'a{1,3}', 'aaab')  # 贪婪模式，['aaa']
re.findall(r'a{1,3}?', 'aaab')  # 非贪婪模式，['a', 'a', 'a']
re.findall(r'a{1,3}+', 'aaab')  # 独占模式，Python内置的re库不支持，会报错 re.error: multiple repeat
```

## 转义字符

常用于转义空白符，空格空格不需要转义，用普通的英文空格表示即可

- `\s` 任意空白符
- `\n` 换行，Windows平台的换行为`\r\n`
- `\r` 回车
- `\f` 换页
- `\t` 制表(水平)
- `\v` 制表(垂直)

像 Python 等大多数语言，反斜杠(`\`)在字符串中表示转义，比如表示斜杠`\/`和反斜杠`\\`

也就是说如果要在 Python 中表示正则的 `\d` 则需要写成：`\\d`，而要表示正则的`\\`，则需要四个反斜杠`\\\\`

为了避免混乱，在 Python 中通常建议使用r前缀来取消字符串本身的转义：`r"\d"`

## RE of Python

> 参考：<https://www.liujiangblog.com/course/python/74>

```python
import re

# 参数说明
"""
pattern 正则表达式
string 要匹配的字符串
flags 匹配模式，多个可用竖线分割：re.I | re.S
"""


# 查找文本，仅返回开头符合规则的对象，匹配则返回re.Match对象，否则返回None
# match(pattern, string, flags=0)
obj = re.match(r"123", "abc123")  # None
obj = re.match(r"abc", "abc123")  # <re.Match object; span=(0, 3), match='abc'>
obj.group()  # "abc"
obj.span()  # (0,3)
obj.start()  # 0
obj.end()  # 3


# 查找文本，仅返回第一处符合规则的对象，其它与match方法相同
# search(pattern, string,flags=0)
obj = re.search(r"789", "abc123def456")  # None
obj = re.search(r"\d{3}", "abc123def456")  # <re.Match object; span=(3, 6), match='123'>
obj.group()  # "123"
obj.span()  # (3,6)


# 查找文本，list形式返回所有符合规则的字符
# findall(pattern, string, flags=0)
re.findall(r"\d{3}", "abc123def456")  # ['123', '456']


# 分割文本，list形式返回，类似字符串的.split()方法
# re.split(pattern, string, maxsplit=0, flags=0)
# 参数maxsplit用于指定分割次数
re.split(r"[\+\-\*\/]", "5+4*3-2/1")  # 从四则运算符处将文本分割：['5', '4', '3', '2', '1']
re.split(r"[\+\-\*\/]", "5+4*3-2/1", maxsplit=2)  # 从四则运算符处将文本分割2次：['5', '4', '3-2/1']
re.split(r"([\+\-\*\/])", "5+4*3-2/1")  # 从四则运算符处将文本分割，并保留运算符(使用正则分组功能实现)：['5', '+', '4', '*', '3', '-', '2', '/', '1']


# 替换文本，返回替换后的新文本，类似字符串的.replace()方法，但是replace不能忽略大小写
# sub(pattern, repl, string, count=0, flags=0)
# 参数count用于指定替换次数，默认=0替换全部
# 参数repl可引用pattern，还可以是自定义函数
re.sub(r"l", "L", "Hello, World!")  # 'HeLLo, WorLd!'
re.sub(r"l", "L", "Hello, World!", count=2)  # 'HeLLo, World!'
re.sub(r"(World)", r"<em>\1<em>", "Hello, World!")  # 'Hello, <em>World<em>!'


# 将正则编译为对象，然后调用以上方法，实现复用提高效率
# compile(pattern, flags=0)
obj = re.compile(r"abc", re.I)  # re.I 忽略大小写
obj.sub("000", "abc123ABc")  # "000123000"
obj.match("abc123").group()  # "abc" 
# 还可以返回迭代器对象，使用group()方法调用
iters = obj.finditer(string)
for i in iters:
    print(i.group())


# 保持特殊符号在正则中的原意
s="#a.b/"
re.escape(s)  # '\\#a\\.b/'
```
