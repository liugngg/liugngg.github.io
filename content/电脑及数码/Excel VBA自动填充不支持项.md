---
date: 2025-09-28
tags:
  - 电脑技术
title: Excel VBA自动填充不支持项
lastmod: 2025-09-28
categories: []
draft: false
---


```vba
Private Sub Worksheet_BeforeDoubleClick(ByVal Target As Range, Cancel As Boolean)
    Application.ScreenUpdating = FALSE
    Application.DisplayAlerts = FALSE
    ’On Error Resume Next
    maxRow = ActiveSheet.UsedRange.Rows.Count
    If Target.Row = 1 And Target.Column = 6 Then
        If MsgBox(“是否将检验结果中的所有空白项” & Chr(10) & “填充为’不适用’？”, vbOKCancel + vbExclamation, “检验结果自动填充”) = vbOK Then
            Call AutoFill
        End If
    ElseIf Target.Row = 1 And Target.Column = 7 Then
        If MsgBox("是否清空所有检验结果？", vbOKCancel + vbExclamation, "检验结果全部清空") = vbOK Then
            Call ClearResult
        End If
        
        '关闭共享文件簿：
        If ActiveWorkbook.MultiUserEditing Then
            If MsgBox("是否取消共享文件簿？", vbOKCancel + vbExclamation, "取消共享文件簿") = vbOK Then
                ThisWorkbook.ExclusiveAccess
            End If
        End If
        
    ElseIf Target.Row = 1 And Target.Column = 1 Then
        If MsgBox("是否开始检验结果的完整性检查？", vbOKCancel + vbExclamation, "检验结果完整性检查") = vbOK Then
            Call CheckDone
        End If
        
        '如果双击检验结果所在列的单元格时，如果当前单元格为本章节的第一项，且值为“符合要求”或“不适用”时，自动填充下面的检验结果
    ElseIf Target.Row < maxRow And Target.Column = 6 Then
        req_pre = Trim(ActiveSheet.Cells(Target.Row - 1, 5).Value)
        result = Trim(Target.Value)
        If req_pre = "" And (result = "符合要求" Or result = "不适用" Or result = "不支持") Then
            For i = Target.Row + 1 To maxRow
                req = Trim(ActiveSheet.Cells(i, 5).Value)
                If req = "" Then
                    Exit For
                Else
                    ActiveSheet.Cells(i, 6).Value = result
                End If
            Next i
        End If
        
    End If
    Application.DisplayAlerts = TRUE
    Application.ScreenUpdating = TRUE
End Sub
Sub AutoFill()
    maxRow = ActiveSheet.UsedRange.Rows.Count
    For i = 2 To maxRow
        unit = Trim(ActiveSheet.Cells(i, 4).Value)
        req = Trim(ActiveSheet.Cells(i, 5).Value)
        result = Trim(ActiveSheet.Cells(i, 6).Value)
        If unit <> “” Or req <> “” Then
            If result = “” Then
                ActiveSheet.Cells(i, 6).Value = "不适用"
            End If
        End If
    Next i
End Sub
Sub ClearResult()
    maxRow = ActiveSheet.UsedRange.Rows.Count
    For i = 2 To maxRow
        unit = Trim(ActiveSheet.Cells(i, 4).Value)
        req = Trim(ActiveSheet.Cells(i, 5).Value)
        If unit <> “” Or req <> “” Then
            ActiveSheet.Cells(i, 6).ClearContents
        End If
    Next i
End Sub
Sub CheckDone()
    maxRow = ActiveSheet.UsedRange.Rows.Count
    For i = 2 To maxRow
        unit = Trim(ActiveSheet.Cells(i, 4).Value)
        req = Trim(ActiveSheet.Cells(i, 5).Value)
        result = Trim(ActiveSheet.Cells(i, 6).Value)
        If unit <> “” Or req <> “” Then
            If unit = “” Or req = “” Or result = “” Then
                msg = “警告：” & Chr(10) & “检验结果中第 " & i & " 行的测试项目发现了空白，” & Chr(10) & "请填充完整后再去生成报告！！"
                MsgBox msg, vbCritical, "检验结果完整性检查"
                End
            End If
        End If
    Next i
    ’设置共享文件簿：
    If Not ActiveWorkbook.MultiUserEditing Then
        ThisWorkbook.SaveAs Filename:=ThisWorkbook.FullName, AccessMode:=xlShared
    End If
    MsgBox “完整性检查通过！” & Chr(13) & “已打开共享工作簿并开始记录所有改动！”, vbInformation, “检验结果完整性检查”
    'ActiveWorkbook.Save
End Sub
```