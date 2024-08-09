---
{"dg-publish":true,"permalink":"//excel/","tags":["excel"]}
---



工具 -> 宏 -> VB 编辑器

![](http://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/2024-08-08-093943.png)


``` excel
Sub RemoveEmptyLineBreaksFromActiveSheet()
    Dim cell As Range
    Dim cellLines As Variant
    Dim i As Long
    Dim result As String

    ' 遍历当前工作表中的所有单元格
    For Each cell In ActiveSheet.UsedRange
        ' 检查单元格是否包含文本
        If Not IsEmpty(cell.Value) And VarType(cell.Value) = vbString Then
            ' 将单元格内容按换行符分割成数组
            cellLines = Split(cell.Value, Chr(10))
            result = ""
            ' 遍历数组并去掉空行
            For i = LBound(cellLines) To UBound(cellLines)
                If Trim(cellLines(i)) <> "" Then
                    If result <> "" Then
                        result = result & Chr(10) & cellLines(i)
                    Else
                        result = cellLines(i)
                    End If
                End If
            Next i
            ' 将处理后的内容写回单元格
            cell.Value = result
        End If
    Next cell
End Sub

```


点击运行