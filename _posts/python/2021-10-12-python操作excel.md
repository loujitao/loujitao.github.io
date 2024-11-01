---
layout: post
title: Python操作表格
date: 2021-10-12
Author: 娄继涛
categories: 语言
tags: [python,工具]
comments: true
---

### 一、简介

Excel中有xls和xlsx两种格式，它们之间的区别是：

1）文件格式不同。xls是一个特有的二进制格式，其核心结构是复合文档类型的结构，而xlsx的核心结构是XML类型的结构，采用的是基于 XML的压缩方式，使其占用的空间更小。xlsx 中最后一个 x 的意义就在于此。

2）版本不同。xls是Excel2003及以前版本生成的文件格式，而xlsx是Excel2007及以后版本生成的文件格式。

3）兼容性不同。xlsx格式是向下兼容的，可兼容xls格式。

### 二、库的选择

当我们要操作**xls格式**文件时，需要使用到**xlrd和xlwt**这两个第三方库；

当我们要操作**xlsx格式**文件时，则需要使用到**openpyxl**第三方库。

| 模块     | 来源     | 读   | 写   | 支持格式 |
| -------- | -------- | ---- | ---- | -------- |
| xlrd     | 第三方库 | √    | ×    | xls      |
| xlwt     | 第三方库 | ×    | √    | xls      |
| openpyxl | 第三方库 | √    | √    | xlsx     |

**补充库**：

得益于辛勤劳作的python大神们，处理excel已经有大量python包，主流代表有：

**xlwings**：简单强大，可替代VBA

**openpyxl**：简单易用，功能广泛；

​               https://openpyxl.readthedocs.io/en/stable/api/openpyxl.html

**pandas**：使用需要结合其他库，数据处理是pandas立身之本；

​           Pandas官方文档：https://pandas.pydata.org/pandas-docs/stable/ ；

​           Pandas中文文档：[https://www.pypandas.cn](https://www.pypandas.cn/)

**win32com**：不仅仅是excel，可以处理office;

**Xlsxwriter**：丰富多样的特性，直接创造一份美观大方的excel，代码即一切；

**DataNitro**：作为插件内嵌到excel中，可替代VBA，在excel中优雅的使用python

**xlutils**：结合xlrd/xlwt，老牌python包，需要注意的是你必须同时安装这三个库



不想使用 GUI 而又希望赋予 Excel 更多的功能，openpyxl 与 xlsxwriter，你可二者选其一；

需要进行科学计算，处理大量数据，建议 pandas+xlsxwriter 或者 pandas+openpyxl；

想要写 Excel 脚本，会 Python 但不会 VBA 的同学，可考虑 xlwings 或 DataNitro；

至于 win32com，不管是功能还是性能都很强大，有 windows 编程经验的同学可以使用。不过它相当于是 windows COM 的封装，自身并没有很完善的文档，新手使用起来略有些痛苦。

你可根据自己的需求和生产环境，选择合适的 Python-Excel 模块。


### 三、安装库

https://www.cnblogs.com/yuxuefeng/articles/9235431.html

```sh
pip install xlrd -i  https://pypi.tuna.tsinghua.edu.cn/simple
pip install xlwt -i  https://pypi.tuna.tsinghua.edu.cn/simple

pip install openpyxl -i  https://pypi.tuna.tsinghua.edu.cn/simple
```

以下代码使用环境为：

工具： pycharm-2018.02 + Anaconda3-2021.05 

python3.7 + openpyxl 3.0.7 +  xlrd 1.2.0 + xlwt 1.3.0

### 四、操作代码

#### XLS文件读写：

```python
# -*- coding: utf-8 -*-
'''
	读写xls文件的代码
'''
import xlrd
import xlwt
from datetime import date,datetime

def read_excel():

  # 打开文件
    workbook01= xlrd.open_workbook("file.xls")
    table01 = workbook01.sheet_by_index(0)
    print(table01.name,table0.nrows,table0.ncols)

  # 根据sheet索引或者名称获取sheet内容
    sheet2 = workbook01.sheet_by_index(0) # sheet索引从0开始
    sheet2 = workbook01.sheet_by_name('sheet2')

  # sheet的名称，行数，列数
    print(sheet2.name,sheet2.nrows,sheet2.ncols)

  # 获取整行和整列的值（数组）
    rows = table0.row_values(3) # 获取第四行内容
    cols01 = table01.col_values(3) # 获取第三列内容
    print(rows)
    print(cols01)

  # 获取单元格内容
    print(table0.cell(1,0).value.encode('utf-8'))
    print(table0.cell_value(1,0).encode('utf-8'))
    print(table0.row(1)[0].value.encode('utf-8'))

  # 获取单元格内容的数据类型
    print(table0.cell(1,0).ctype)

def main():
    read_excel()

if __name__ == '__main__':
    main()
```



#### xlsx文件读写

```python
from openpyxl import load_workbook
 
# 工作簿 workbook 表单 sheet 单元格 cell
#打开指定工作簿    load_workbook()
wb=load_workbook('test_data.xlsx')

# 定位到指定的表单    workbook['表单名']
sheet=wb['test_data']

#读取指定单元格   cell(row, column).value
cell_values = sheet.cell(2,3).value
print(cell_values)

# 写入单元格
sheet.cell(2,3).value = '赋值给单元格'

#写入多行内容到一个单元格内
sheet['K8'] = '与源表尽量保持一致\n如源表缺失须补充'
sheet['K8'].alignment = Alignment(wrapText=True)

#获取最大的行数
row=sheet.max_row
# 获取最大列数
col=sheet.max_column
print(row,col)
# 修改Excel之后，保存    workbook.save('文件名')
wb.save('test_data.xlsx')

#删除表单   
del workbook['表单名']
workbook.remove('表单名')

sheet.max_row    #获取表单数据的总行数
sheet.max_column     #获取表单数据的总列数
sheet.rows    #获取按行所有的数据
sheet.columns    #获取按列所有的数据

# 创建一个工作簿
workbook = openpyxl.Workbook()
# 创建一个表单
sheet = workbook.create_sheet('表单1')
# 写入一个数据
sheet.cell(row=1, column=1, value="python")
# 保存
workbook.save('test.xlsx')

```

从excel中读取出来的数据只有两种类型，即**数值类型**和**字符串类型**;

不要随便在表格中敲空格，会影响判断最大行数和最大列数;

运行操作excel的代码时，要先关闭在操作系统中打开的相关excel表，否则可能会无法读取/写入数据;



**字体样式**

openpyxl里面有专门的字体样式，可以通过以下方式导入，使用的代码如下：

```python
from openpyxl.styles import Font, PatternFill, Border, Side, Alignment

#这里创建的字体为微软雅黑，字体大小为10，不加粗，没有斜体，没有下划线，没有删除线，颜色为黑色。
font = Font(name='微软雅黑', size=10, bold=False, italic=False, vertAlign=None,underline='none', strike=False, color='FF000000')

#将样式设置到单元格中去
sheet['A1'].font=font
sheet.cell(1,1).font=font
```

**填充样式**

```python
#它的图案类型为darkUp，开始颜色行色，结束颜色为红色，红黄相间的格栅化样式填充
fill = PatternFill(fill_type='darkUp',start_color='FFFF00',end_color='FF0000')
sheet.cell(1,1).fill=fill

GradientFill(stop=(渐变颜色 1，渐变颜色 2……))

.row_dimensions[行编号].height = 行高
.column_dimensions[列编号].width = 列宽
```

**边框样式**

```python
#虚线，上红，下绿，左紫，右黑
border = Border(left=Side(border_style='dashDotDot',color='9932CC'),
                right=Side(border_style='dashDotDot',color='121212'),
                top=Side(border_style='dashDotDot',color='8B0A50'),
                bottom=Side(border_style='dashDotDot',color='B3EE3A'),)
sheet.cell(5,4).border=border
```



**对齐样式**

```python
#水平居中，垂直居中，文字旋转角度为0，缩进为0
alignment = Alignment(horizontal='center',
                    vertical='center',
                    text_rotation=0,
                    indent=0)
 
sheet.cell(5,3).alignment=alignment
```



**计算公式**

```python
#只要你知道Excel中的计算和公式，就可以直接套用到代码中
sheet['F1']='=SUM(A1:E1)'
```



**单元格的合并与拆分**

```python
.merge_cells(待合并的格子编号)
.merge_cells(start_row=起始行号，start_column=起始列号，end_row=结束行号，end_column=结束列号)

#合并单元格  合并了A1到E1的所有单元格
sheet.merge_cells('A1:E1')
sheet.merge_cells(start_row=7,start_column=1,end_row=8,end_column=3)

.unmerge_cells(待合并的格子编号)
.unmerge_cells(start_row=起始行号，start_column=起始列号，end_row=结束行号，end_column=结束列号)

#拆分单元格
sheet.unmerge_cells('A1:E1')
```



**格式刷功能**  ：  复制单元格样式，应用到新单元格

```python
from copy import copy
'''
    复制源头单元格样式，应用到目标单元格
'''
def copy_style(self, source_cell, target_cell):
    if source_cell.has_style:
        target_cell._style = copy(source_cell._style)
        target_cell.font = copy(source_cell.font)
        target_cell.border = copy(source_cell.border)
        target_cell.fill = copy(source_cell.fill)
        target_cell.number_format = copy(source_cell.number_format)
        target_cell.protection = copy(source_cell.protection)
        target_cell.alignment = copy(source_cell.alignment)
     if source_cell.hyperlink:
        target_cell._hyperlink = copy(source_cell.hyperlink)
     if source_cell.comment:
         target_cell.comment = copy(source_cell.comment)
            
#使用示例：         
copy_style(self.sheet['E1'], self.sheet['J1'])

```



完整示例可以参考代码：

https://github.com/loujitao/pyUtils/blob/master/txtFile/excel_addSRC.py