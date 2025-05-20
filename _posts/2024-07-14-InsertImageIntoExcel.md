---
layout: single
title: "[Visual Basic] Insert Image Into Excel."
categories: Visual_Basic
tag: [Visual_Basic]
toc: true
toc_sticky: true
---

### 엑셀 노가다 대신해줄 프로그램
비슷한 프로그램을 여러번 만들어봤지만, 각 사이트마다 미팅에서 사용되는 파일의 포맷이 다르기에.. 상황에 맞춰 새로 작성해야 하는일이 있다.

이번에는 중국 후이저우 사이트에서 사용되는 엑셀에 이미지와 Defect Feature를 대신 삽입해주는 매크로 프로그램이다.


```vb
Imports Excel = Microsoft.Office.Interop.Excel
Imports System.IO

Public Class Form1

    Private Const CGID_Col As Integer = 3
    Private Const Pattern_Col As Integer = 4
    Private Const Cam_Col As Integer = 2
    Private Const DefectType_Col As Integer = 6
    Private Const Grid_Col As Integer = 7
    Private Const PNL_X_Col As Integer = 8
    Private Const PNL_Y_Col As Integer = 9


    Public Structure CSVData
        Public FilePath As String
        Public Rows As Integer
        Public Columns As Integer
        Public Data As String(,)
    End Structure
    Private Sub btnInsertImage_Click(sender As Object, e As EventArgs) Handles btnInsertImage.Click

        ' Excel 파일을 엽니다
        Dim filePath As String = String.Empty
        openFileDialog.Filter = "Excel Files|*.xls;*.xlsx;*.xlsm"
        openFileDialog.Title = "Select an Excel File"

        If openFileDialog.ShowDialog() = DialogResult.OK Then
            filePath = openFileDialog.FileName
        Else
            MessageBox.Show("파일을 선택하지 않았습니다.", "정보", MessageBoxButtons.OK, MessageBoxIcon.Information)
            Return
        End If

        ' Excel 애플리케이션을 시작합니다
        Dim excelApp As New Excel.Application
        Dim workbook As Excel.Workbook = Nothing
        Dim worksheet As Excel.Worksheet = Nothing

        Dim Count As Integer = 0

        Try
            workbook = excelApp.Workbooks.Open(filePath)

            Dim sheetName As String = TextBox_SheetName.Text
            worksheet = DirectCast(workbook.Sheets(sheetName), Excel.Worksheet)

            Dim cameraPaths As String() = {TextBox_CAM1.Text, TextBox_CAM2.Text, TextBox_CAM3.Text}
            Dim dataList_CAM As List(Of CSVData)() = {New List(Of CSVData), New List(Of CSVData), New List(Of CSVData)}
            For i = 1 To 3
                Dim directoryPath_LOT As String = cameraPaths(i - 1) & "\LOT\"
                Dim csvFiles As String() = Directory.GetFiles(directoryPath_LOT, "*.csv")

                For Each filePath_LOT As String In csvFiles
                    Dim csvData As New CSVData With {
                        .FilePath = filePath_LOT
                    }

                    Try
                        ReadCSVFile(csvData)
                        dataList_CAM(i - 1).Add(csvData)
                    Catch ex As Exception
                        MessageBox.Show("파일을 읽는 중 오류가 발생했습니다: " & ex.Message)
                    End Try

                Next
            Next

            For i = 2 To 999
                TextBox_NumberOfOperation.Text = i

                Dim CellName As String

                'CGID
                CellName = "A" & i
                If worksheet.Range(CellName).Value = "" Then '예외처리
                    Exit For
                End If
                Dim CGID As String = worksheet.Range(CellName).Value.ToString()
                TextBox_CGID.Text = CGID

                'Pattern
                Dim Pattern As String = ComboBox_Pattern.Text

                'CAM
                CellName = "P" & i
                Dim CAM As String = worksheet.Range(CellName).Value.ToString()
                TextBox_CAM.Text = CAM

                'DefectType
                Dim DefectType = ComboBox_DefectType.Text

                'Grid
                CellName = "H" & i
                Dim Grid As String = worksheet.Range(CellName).Value.ToString()
                TextBox_Grid.Text = Grid

                'PNL_X
                CellName = "Q" & i
                Dim PNL_X As String = worksheet.Range(CellName).Value.ToString()
                TextBox_PNL_X.Text = PNL_X

                'PNL_Y
                CellName = "R" & i
                Dim PNL_Y As String = worksheet.Range(CellName).Value.ToString()
                TextBox_PNL_Y.Text = PNL_Y

                'keyWord Merge
                Dim keyWord_CropImage = System.IO.Path.Combine(CGID & "_" & Pattern & "_" & Cam & "_" & DefectType & "_" & Grid)
                Dim keyWord_SumImage = System.IO.Path.Combine(CGID & "_" & DefectType & "_" & Grid & "_" & PNL_X & "_" & PNL_Y & "_[" & Pattern & "]_")

                Dim directoryPath_CropImage As String = ""
                Dim CamIndex As Integer = 0
                If Cam = "L" Then
                    directoryPath_CropImage = TextBox_CAM1.Text & "\PANEL\" & CGID
                    CamIndex = 0
                ElseIf Cam = "C" Then
                    directoryPath_CropImage = TextBox_CAM2.Text & "\PANEL\" & CGID
                    CamIndex = 1
                ElseIf Cam = "R" Then
                    directoryPath_CropImage = TextBox_CAM3.Text & "\PANEL\" & CGID
                    CamIndex = 2
                End If

                Dim directoryPath_SumImage As String = System.IO.Path.Combine(directoryPath_CropImage & "\Sum")

                Dim foundImagePath_CropImage As String = FindImageWithKeyword(directoryPath_CropImage, keyWord_CropImage, False)
                Dim foundImagePath_CropImage_DFT As String = FindImageWithKeyword(directoryPath_CropImage, keyWord_CropImage, True)
                Dim foundImagePath_SumImage As String = FindImageWithKeyword(directoryPath_SumImage, keyWord_SumImage, False)

                If foundImagePath_CropImage = "" Or foundImagePath_SumImage = "" Then
                    GoTo ContinueLoop
                End If
                ' 이미지 파일 경로와 삽입할 위치를 설정합니다
                CellName = "S" & i
                Dim targetRange_CropImage As Excel.Range = worksheet.Range(CellName) 'CropImage 삽입위치
                CellName = "T" & i
                Dim targetRange_CropImage_DFT As Excel.Range = worksheet.Range(CellName) 'CropImage 삽입위치
                CellName = "U" & i
                Dim targetRange_SumImage As Excel.Range = worksheet.Range(CellName) ' SumImage 삽입위치

                worksheet.Shapes.AddPicture(foundImagePath_CropImage,
                                    Microsoft.Office.Core.MsoTriState.msoFalse,
                                    Microsoft.Office.Core.MsoTriState.msoCTrue,
                                    targetRange_CropImage.Left, targetRange_CropImage.Top, -1, -1)

                worksheet.Shapes.AddPicture(foundImagePath_CropImage_DFT,
                                    Microsoft.Office.Core.MsoTriState.msoFalse,
                                    Microsoft.Office.Core.MsoTriState.msoCTrue,
                                    targetRange_CropImage_DFT.Left, targetRange_CropImage_DFT.Top, -1, -1)

                worksheet.Shapes.AddPicture(foundImagePath_SumImage,
                                    Microsoft.Office.Core.MsoTriState.msoFalse,
                                    Microsoft.Office.Core.MsoTriState.msoCTrue,
                                    targetRange_SumImage.Left, targetRange_SumImage.Top, -1, -1)

                ''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
                'Feature 삽입
                For Each csvData In dataList_CAM(CamIndex)
                    For row = 0 To csvData.Rows - 1
                        If csvData.Data(row, CGID_Col) = CGID And
                                csvData.Data(row, Pattern_Col) = Pattern And
                                csvData.Data(row, Cam_Col) = (CamIndex + 1).ToString() And
                                csvData.Data(row, DefectType_Col) = DefectType And
                                csvData.Data(row, Grid_Col) = Grid And
                                csvData.Data(row, PNL_X_Col) = PNL_X And
                                csvData.Data(row, PNL_Y_Col) = PNL_Y Then
                            For col = 0 To csvData.Columns - 1
                                worksheet.Cells(i, col + 22).Value = csvData.Data(row, col)
                            Next
                            GoTo ContinueLoop_csvData
                        End If
                    Next
ContinueLoop_csvData:
                Next
                ''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

                Count = Count + 1
ContinueLoop:
            Next i

            Dim savePath As String = filePath.Substring(0, filePath.LastIndexOf("."))
            savePath = System.IO.Path.Combine(savePath & "_Img.xlsx")
            workbook.SaveAs(savePath)

        Catch ex As Exception
            MessageBox.Show("오류가 발생했습니다: " & ex.Message, "오류", MessageBoxButtons.OK, MessageBoxIcon.Error)
        Finally
            ' 모든 객체 해제
            ReleaseComObject(worksheet)
            If workbook IsNot Nothing Then workbook.Close(False)
            ReleaseComObject(workbook)
            If excelApp IsNot Nothing Then
                excelApp.Quit()
                ReleaseComObject(excelApp)
            End If
        End Try

        MessageBox.Show(Count & "개의 작업이 성공적으로 완료되었습니다.", "완료", MessageBoxButtons.OK, MessageBoxIcon.Information)
    End Sub

    Private Sub ReleaseComObject(ByVal obj As Object)
        Try
            System.Runtime.InteropServices.Marshal.ReleaseComObject(obj)
            obj = Nothing
        Catch ex As Exception
            obj = Nothing
        Finally
            GC.Collect()
        End Try
    End Sub

    Private Function FindImageWithKeyword(directoryPath As String, keyword As String, IsDFT As Boolean) As String
        Dim file As String
        Dim filePath As String
        'Dim foundFilePath As String

        ' 디렉토리 내 모든 파일을 탐색합니다.
        file = Dir(directoryPath & "\*.*")

        Do While file <> ""
            ' 파일의 전체 경로를 구성합니다.
            filePath = directoryPath & "\" & file

            ' 파일이 이미지 파일인지 확인합니다 (예: JPEG 파일).
            If IsImageFile(filePath) Then
                ' 파일 이름에 특정 키워드가 포함되어 있는지 확인합니다.
                If InStr(filePath, keyword) > 0 Then
                    If IsDFT = True Then 'DFT 인데
                        If Not InStr(filePath, "_Cam") > 0 Then ' _Cam 이 없다면
                            GoTo NextFile ' Continue
                        End If
                    End If
                    ' 특정 키워드를 포함하고 있는 이미지 파일의 경로를 반환합니다.
                    FindImageWithKeyword = filePath
                    Exit Function
                End If
            End If
NextFile:
            ' 다음 파일을 탐색합니다.
            file = Dir()
        Loop

        ' 특정 키워드를 포함하는 이미지 파일을 찾지 못한 경우 빈 문자열을 반환합니다.
        FindImageWithKeyword = ""
    End Function

    Private Function IsImageFile(filePath As String) As Boolean
        Dim fileExt As String
        fileExt = UCase(Mid(filePath, InStrRev(filePath, ".") + 1))

        ' 이미지 파일 확장자를 여기에 추가합니다 (예: JPEG, PNG 등).
        Select Case fileExt
            Case "JPG", "JPEG", "PNG", "BMP", "GIF"
                IsImageFile = True
            Case Else
                IsImageFile = False
        End Select
    End Function
    Private Sub ReadCSVFile(ByRef csvData As CSVData)
        Try
            ' CSV 파일을 읽어옵니다.
            Using reader As New StreamReader(csvData.FilePath)
                Dim lines As New List(Of String)()

                ' 모든 줄을 읽어옵니다.
                Dim line As String = reader.ReadLine()
                While line IsNot Nothing
                    lines.Add(line)
                    line = reader.ReadLine()
                End While

                ' 첫 번째 줄을 기준으로 열 수를 설정합니다.
                Dim firstLineCells() As String = lines(0).Split(","c)
                csvData.Columns = firstLineCells.Length
                csvData.Rows = lines.Count

                ' 데이터를 담을 배열을 초기화합니다.
                csvData.Data = New String(csvData.Rows - 1, csvData.Columns - 1) {}

                ' 데이터를 배열에 저장합니다.
                For i As Integer = 0 To lines.Count - 1
                    Dim cells() As String = lines(i).Split(","c)
                    For j As Integer = 0 To csvData.Columns - 1
                        csvData.Data(i, j) = cells(j)
                    Next
                Next
            End Using

        Catch ex As Exception
            MessageBox.Show($"파일을 읽는 도중 오류가 발생했습니다: {ex.Message}")
        End Try
    End Sub

End Class

```

끝.