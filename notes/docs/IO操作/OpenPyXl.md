# OpenPyXl

```python
import time
import openpyxl
from openpyxl.styles import PatternFill


# 获取时间戳
stp = int(time.time())


class HandleXlsx():
    def __init__(self, file_name):
        self.file_name = file_name
        # Workbook句柄
        self.wb = openpyxl.load_workbook(self.file_name)
        # Sheet句柄
        self.shs = self.wb.sheetnames
        self.sh_old = self.wb[self.shs[0]]
        self.max_row = self.sh_old.max_row
        self.max_col = self.sh_old.max_column

        # 新建excel
        self.wb_copy = openpyxl.Workbook()

        self.ws = self.copy_sheet()
        self.ws.sheet_properties.tabColor = "4b5cc4"


    # 获取真实row行数
    def get_real_rows_num(self):
        num = 0
        while self.max_row > 0:
            value = self.read_cell_value(self.max_row, 1)
            if value == None:
                self.max_row = self.max_row -1
            else:
                num = self.max_row
                break

        return num

    # 获取单元格内容，传入行号和列号，都是从1开始的
    def read_cell_value(self, row, column):
        return self.ws.cell(row, column).value

    # row_index是python的计数方式，从0开始，row是Excel的计数方式
    # 获取一行的数据
    def row_anyone(self, row_index):
        # 所有行，得到生成器对象
        all_rows = self.ws.rows
        # 将生成器转换为list后，索引，得到任意一行
        any_row = list(all_rows)[row_index]  # <class 'tuple'>
        # 上面得到是类对象的集合，还需要转换为具体值的集合
        row_list = []
        for cell in any_row:
            row_list.append(cell.value)
        return row_list

    # 获取一列的数据(同获取一行)
    def col_anyone(self, col_index):
        all_cols = self.ws.columns
        any_col = list(all_cols)[col_index]
        col_list = []
        for cell in any_col:
            col_list.append(cell.value)
        return col_list

    # 根据任一列的任一cell获取行号
    def row_index(self, col_index, any_cell):
        # 迭代某一列
        row_index = 1
        max_index = self.get_real_rows_num()
        for cell in self.col_anyone(col_index):
            if cell == any_cell:
                return row_index
            row_index += 1
            if row_index > max_index:
                return None

    # 根据任一行的任一cell获取列号
    def col_index(self, row_index, any_cell):
        # 迭代某一列
        col_index = 1
        # max_index = self.get_max_column_num()
        for cell in self.row_anyone(row_index):
            if cell == any_cell:
                return col_index
            col_index += 1
            if col_index > self.max_col:
                return None

    # Copy Excel
    def copy_sheet(self):
        # 新建sheet
        # sh_new = wb_copy.create_sheet(str(stp))

        # Sheet句柄
        shs_new = self.wb_copy.sheetnames
        sh_new = self.wb_copy[shs_new[0]]

        # 把wb中的sheet复制wb_copy中的sheet
        for m in range(1,self.max_row+1):
            for n in range(97,97+self.max_col):  # chr(97)='a'
                n = chr(n)  # ASCII字符
                i = f'{n}{m}'  # 单元格编号
                cell = self.sh_old[i].value  # 获取旧sheet的值，还可以：cell = sh0.cell(row=m, column=n)
                sh_new[i] = cell  # 赋值到新sheet
        # 保存修改
        self.wb_copy.save(f"result_{str(stp)}.xlsx")
        self.wb_copy.close()

        return sh_new


    # 向单元格写入内容，并着色
    def write_cell_value(self, row, column, value, color=None):
        cell = self.ws.cell(row=row, column=column)
        cell.value = value
        if color:
            cell.fill = PatternFill(fill_type="solid", fgColor=color)

        self.wb_copy.save(f"result_{str(stp)}.xlsx")
        self.wb_copy.close()


if __name__ == "__main__":
    h = HandleXlsx("data/case/infoq.xlsx")
    # s = h.row_anyone(2)[2]
    # s = h.col_anyone(2)
    # s = h.row_index(0, 3)
    s = h.col_index(0, "is_test")
    print(s)
```
