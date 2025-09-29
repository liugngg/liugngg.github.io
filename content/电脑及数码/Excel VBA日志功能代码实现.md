---
date: 2025-09-28
tags:
  - 电脑技术
title: Excel VBA日志功能代码实现
lastmod: 2025-09-28
categories: []
draft: false
---



```
Dim old_str
Private Sub Workbook_SheetChange(ByVal Sh As Object, ByVal Target As Range)
    On Error Resume Next
    Dim new_str
    new_str = Target.Value
    If Sh.Name <> “日志” And Target.Rows.Count = 1 Then
        With Sheets(“日志”)
            If old_str <> new_str Then
                ROW1 = Sheets(“日志”).[A65536].End(xlUp).Row + 1
                ’操作时间
                .Cells(ROW1, 1) = Format(Now, “yyyy-mm-dd hh:mm:ss”)
                '工作表名称
                .Cells(ROW1, 2) = Sh.Name
                ’操作的单元格
                .Cells(ROW1, 3) = Target.Address
                ’原值
                .Cells(ROW1, 4) = old_str
                ’修改后的值
                .Cells(ROW1, 5) = new_str
                ’当前Excel用户名称
                .Cells(ROW1, 6) = ActiveWorkbook.UserStatus(1, 1)
            End If
        End With
    End If
End Sub
Private Sub Workbook_SheetSelectionChange(ByVal Sh As Object, ByVal Target As Range)
    On Error Resume Next
    If Target.Rows.Count = 1 Then
        old_str = Target.Value
    End If
End Sub
```