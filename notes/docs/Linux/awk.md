# AWK

用于数据分析并生成报告

> 并不会对原文件进行修改，需要使用`awk '{print $0 > "filename"}' filename`的方式修改原文件

https://book.saubcy.com/AwkInAction/

{**这里面放的是处理每一行时要执行的语句，主输入循环**}

- `awk ‘条件 {函数; print $0}’ filename`
  - 原样输出要放在双引号里：`{print "str"}`
  - 带编号输出：`{print x++,$0}`
- `awk -f script.awk filename`

BEGIN{**这里面放的是执行前的语句，可以做预处理**}

- 指定输入输出分隔符：`awk ‘BEGIN{FS=":";OFS="-"} {print $0}’ filename`
  - 指定多个可用中括号扩起来：`awk ‘BEGIN{FS="[:;]"} {print $0}’ filename`
- 其它方式：`awk -F ':' ‘{print $0}’ OFS="-" filename`

END{**这里面放的是处理完所有的行后要执行的语句**}

## 系统变量

- 字段，被空格制表符等分隔开的词组，可以指定分隔符
- 记录，每行称作记录

### 字段相关

- $1..$n表示第几字段，$0表示整行
- FS(font segmentation)输入字段分隔符，默认为空格或制表符
- OFS(out ~)输出字段分隔符，用于打印时分隔字段，默认为空格
- NF(number font)字段数量
  - 最后一个字段`$NF`
  - 倒数第二个字段`$(NF-1)`

### 记录相关

- RS(record segmentation)输入记录分隔符，默认为换行符
- ORS(out ~)：输出记录分隔符，用于打印时分隔记录，默认为换行符
  - 指定输入分隔符方式1(比如:)：`awk ‘BEGIN{FS=":"} {print $0}’ filename`
  - 指定输入分隔符方式2(比如;)：`awk  ‘{print $0}’ filename`
  - 指定输入多个分隔符(比如:和;)：`awk -F '[:;]' ‘{print $0}’ filename`
- NR(number record) 记录数量，多个文件累加计算
  - NR=1 表示加上表头
  - NR!=1 表示不处理表头
- FNR 记录数量，多个文件单独计算

### 其它

- FILENAME：当前文件名
- OFMT：数字输出的格式，默认为％.6g

## 表达式

- 赋值操作符：=，++，--，+=
- 算数操作符：+，-，*，/，%，^
- 关系操作符：==, !=, >, <, >=, <=
- 布尔操作符：&&, ||, !

## 条件

### 正则表达式

- ~ 表示开始。/ /中是正则表达式
- ! 表示取反。也可以用!/WAIT/

### 判断

`awk ‘{if(条件) 语句1; else if() 语句2; else 语句3}’ filename`

如果同一条件想对多个语句生效，多个语句需要用{}扩起来：  
`awk ‘{if(条件) {语句1;语句2}}’ filename`

### 循环

`awk '{while() 语句}'`

`awk '{do 语句}while()'`

`awk '{for(初始值; 循环判断条件; 累加) 语句}'`

`break`

`continue`

## 函数

如何使用：`man awk`，然后`/函数名`搜索相关说明

### 算数函数

```text
sin()：正弦
cos()：余弦
sqrt()：平方根
rand()：随机数
```

### 字符串函数

r 用于匹配的正则表达式
s 替换后字符串值
t 替换范围，目标字符串，可选参数，默认使用$0

#### 替换函数

- sub(r,s,t)
  - 局部替换，只替换一行之中第一个匹配到的字符串

- gsub(old,new,替换范围)
  - 全局替换，所有匹配到的字符串都会替换

```text
substr(s,p,n)

index(s,t)
length(s)：返回字符串长度
match(s,r)
split(s,t,sep)
toupper()：字符转为大写
tolower()：字符转为小写
```

### 自定义函数

```text
function 函数名(参数){
    语句
    return 变量
}
```

## 其它用法

字段求和/求平均值等：`awk '{sum=0; for(c=2; c<=NF; c++) sum += $c; print sum/(NF-1)}' filename`

[拆分文件](https://coolshell.cn/articles/9070.html)
