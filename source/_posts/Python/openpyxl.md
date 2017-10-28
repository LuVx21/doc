---
title: openpyxl
tags:
- Python
- openpyxl
---

# openpyxl中cell数据类型

* NULL
* 字符串
* 数字

# import module

```python
from openpyxl import load_workbook
from openpyxl import Workbook
```
# obj of workbook

```python
workbook = load_workbook(u"1.xlsx")
# 新建文件常用
workbookNew = Workbook()
```

# obj of sheet

```python
# 方式1:name
sheet = workbook["sheet1"]
# 方式2:index
sheetnames = workbook.get_sheet_names()
sheet = workbook.get_sheet_by_name(sheetnames[0])
# 方式3
sheet = workbook.active
```

部分属性
```
sheet.title
sheet.max_row
sheet.max_column
```

## new sheet

```python
# 创建sheet可以指定位置,但默认以sheet名排序
sheet1 = workbook.create_sheet(0)
sheet1.title = "sheetname"
# 合并
sheet2 = workbook.create_sheet(title="sheetname2")
```

## sheet
```python
# RRGGBB
sheet.sheet_properties.tabColor = "000000"
```

# cell

```python
# 行列均从1计数
print(sheet.cell(row = 1 , column = 1).value)
sheet['A1'] = '47'
# 指定参数,大胆推测,可以传进字典
sheet.cell(row = 1 , column = 1, value = '47')
# 另存为,自动创建新文件
workbook.save(u"2.xlsx")
```

## 读取多个cell

```python
cells = ws['A1':'C3']
```

# 遍历

```python
rows = sheet.rows
columns = sheet.columns
# or↓
for row in sheet.iter_rows('A1:C3'):
    for cell in row:
        print(cell)
```


# 参考

[openpyxl](http://openpyxl.readthedocs.io/en/default/tutorial.html)