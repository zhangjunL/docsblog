# Python办公自动化（一）对比execl内容
## 安装依赖
需要安装的库：openpyxl，pandas
如何安装：
打开命令行（win+R 输入cmd/powershell），输入以下命令
```bash
pip install openpyxl 
pip install pandas
```
## 代码 
新建一个文件夹 ，新建一个文件，文件名为compare.py，输入以下代码，保存。
```python
#使用说明
#1.将df1.xlsx和df2.xlsx分别替换为你的文件名
#2.将diff替换为你的工作表名
#3.将机构名称替换为你的列名，此处意思是将两个表格中的机构名称相同的数据合并,并比较两个表格中的机构名称相同的数据是否相同

from openpyxl import Workbook, load_workbook
from openpyxl.styles import PatternFill, Font

import pandas as pd

# 加载Excel数据到Pandas DataFrame中
# 将df1.xlsx和df2.xlsx分别替换为你的文件名
# 将diff替换为你的工作表名
df1 = pd.read_excel('df1.xlsx', sheet_name='diff')
df2 = pd.read_excel('df2.xlsx', sheet_name='diff')
df1 = df1.drop_duplicates()
df2= df2.drop_duplicates()
# 合并DataFrame，并按机构名称排序
merged_df = pd.merge(df1, df2, on='名称', suffixes=['_a', '_b'],how='outer').sort_values('名称')
# 将合并后的DataFrame写入到Excel文件中
merged_df.to_excel('merged.xlsx', index=False)

# 设置标记不同值的字体和填充样式
font_red = Font(color='FF0000')
fill_red = PatternFill(start_color='FFC7CE', end_color='FFC7CE', fill_type='solid')

# 打开Excel文件并获取活动工作表
workbook = load_workbook(filename='merged.xlsx')
worksheet = workbook.active

# 将表头添加到Excel文件
for col_num, header in enumerate(merged_df.columns):
    cell = worksheet.cell(row=1, column=col_num+1)
    cell.value = header

# 遍历每一行并比较单元格的值
for row_num, row in enumerate(merged_df.values, start=2):
    for col_num, value in enumerate(row, start=1):
        col_name = merged_df.columns[col_num - 1]
        if col_name.endswith('_a'):
            compare_col_name = col_name[:-2] + '_b'
            compare_value = row[merged_df.columns.get_loc(compare_col_name)]
            if pd.isna(value) and pd.isna(compare_value):
                continue
            if value != compare_value:
                cell = worksheet.cell(row=row_num, column=col_num)
                cell.font = font_red
                cell.fill = fill_red

# 保存Excel文件并关闭工作簿
workbook.save("merged.xlsx")
workbook.close()
```

执行代码
打开命令行（win+R 输入cmd/powershell），进入到该文件夹，输入python compare.py，回车。
## 效果
 merged.xlsx 中为合并后的数据，红色字体为不同的数据

