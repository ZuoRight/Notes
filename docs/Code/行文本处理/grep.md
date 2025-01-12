# GREP

Global Regular Expression Print，一种强大的文本搜索工具

```shell
grep options pattern files

# options 选项
'
-n：显示匹配行及其行号
-v：反转匹配，即显示不包含pattern的行
-i：忽略大小写
-c：计数，显示包含模式的行数
-l：仅列出包含匹配的文件名，而不显示匹配行
-r：递归搜索，查找所有子目录中的文件
-E：使用扩展正则表达式
'

grep -i "example" file1.txt file2.txt  # 搜索多个文件中的文本，并忽略大小写
grep -c 17311112222 demo.log  # 统计log中出现手机号的次数
```
