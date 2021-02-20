# OpenPyXL

复制sheet到新表

```python
import openpyxl


wb0 = openpyxl.load_workbook('a.xlsx')

# 新建sheet
sh_new = wb0.create_sheet()

shs = wb0.sheetnames  # 获取sheet页

sh0 = wb0[shs[0]]
max_row = sh0.max_row  # 最大行数
max_column = sh0.max_column  # 最大列数


# # 新建Excel
# wb1=openpyxl.Workbook()
# wb1.save('test.xlsx')
# wb1=openpyxl.load_workbook('test.xlsx')
# sheets1=wb1.sheetnames
# sh_new=wb1[sheets1[0]]


for m in range(1,max_row+1):
    for n in range(97,97+max_column):  # chr(97)='a'
        n = chr(n)  # ASCII字符
        i = f'{n}{m}'  # 单元格编号
        cell = sh0[i].value  # 获取旧sheet的值，还可以：cell = sh0.cell(row=m, column=n)
        sh_new[i] = cell  # 赋值到新sheet


# wb1.save('test.xlsx')
# wb1.close()

wb0.save("a.xlsx")
wb0.close()
```
