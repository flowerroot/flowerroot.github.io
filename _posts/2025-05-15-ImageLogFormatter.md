---
layout: single
title: "[Visual Basic] Image Log Formatter."
categories: Visual_Basic
tag: [Visual_Basic]
toc: true
toc_sticky: true
---

### 엑셀 자동화 매크로로
중국 후이저우 사이트에서 고객사 직원이 사용할 매크로 프로그램이다.

기존에 만들었던거 소개하면서 이렇게 저렇게 사용하시면 됩니다 라고 말했는데 불합리하다고 개선해달라는 요청을 받아서.. 그냥 새로 다시 하나 짰다.

운영PC의 log에 저장되는 iamge path 정보를 활용하여 image를 발췌하고 삽입하는 방식이다.

Defect의 기본정보와 상세 Feature도 참조해야 하기 때문에 운영, 서버, 검사 PC의 모든 log 파일을 참고하여 새로 파일을 하나 작성해주는.. 과검 분석 Tool로써 사용될 매크로 프로그램이다.

근데 아마 과검분석을 함에 있어서 원본 src 이미지만으로는 Defect의 시인성이 좋지 않기 때문에 전처리 된 dft crop image를 추가하는 방향으로 수정될 것이라.. 예상해본다.

아마도 고객사 직원이 그런 요청을 할 것 같다.

아래는 소스코드이다.

```vb
Imports Excel = Microsoft.Office.Interop.Excel
Imports System.IO

Public Class ImageLogFormatter



    ' 이미지 삽입 함수
    Private Sub InsertImage(IMG_PATH As String, worksheet As Excel.Worksheet, rowNumber As Integer)
        If File.Exists(IMG_PATH) Then
            Dim destDir As String = Path.Combine(Application.StartupPath, "CopiedImages")
            Directory.CreateDirectory(destDir)

            Dim destPath As String = Path.Combine(destDir, Path.GetFileName(IMG_PATH))
            File.Copy(IMG_PATH, destPath, True)

            Dim picture As Excel.Picture = worksheet.Pictures().Insert(destPath)
            picture.Left = worksheet.Cells(rowNumber, 21).Left ' 21열에 삽입
            picture.Top = worksheet.Cells(rowNumber, 21).Top

            ' 셀 크기에 맞게 이미지 크기 조정
            Dim targetCell As Excel.Range = worksheet.Cells(rowNumber, 21)
            picture.Width = targetCell.Width
            picture.Height = targetCell.Height

            ' Placement 속성 변경: 위치와 크기 변경
            picture.Placement = Excel.XlPlacement.xlMoveAndSize
        Else
            Debug.WriteLine($"이미지 파일 없음: {IMG_PATH}")
        End If
    End Sub

    ' CAM 데이터 로딩 함수
    Private Sub LoadCamData(filePath As String, ByRef camData As Dictionary(Of String, String()))
        Dim lines = File.ReadAllLines(filePath)
        For i = 1 To lines.Length - 1
            Dim parts = lines(i).Split(","c)
            Dim CGID = parts(3)
            If Not camData.ContainsKey(CGID) Then camData(CGID) = parts
        Next
    End Sub

    Private Sub ReleaseObject(ByVal obj As Object)
        Try
            If obj IsNot Nothing Then
                System.Runtime.InteropServices.Marshal.ReleaseComObject(obj)
                obj = Nothing
            End If
        Catch ex As Exception
            obj = Nothing
        Finally
            GC.Collect()
        End Try
    End Sub

    Private Sub Button_Start_Click(sender As Object, e As EventArgs) Handles Button_Start.Click
        ' 경로
        Dim OperatingFilePath As String = TextBox_운영PC.Text
        Dim ServerFilePath As String = TextBox_서버PC.Text
        Dim CAM1FilePath As String = TextBox_CAM1.Text
        Dim CAM2FilePath As String = TextBox_CAM2.Text
        Dim CAM3FilePath As String = TextBox_CAM3.Text

        ' 운영 데이터 Dictionary 로딩 (PANEL_ID 기준)
        Dim operatingData As New Dictionary(Of String, String())
        Dim operatingLines = File.ReadAllLines(OperatingFilePath)
        For i = 1 To operatingLines.Length - 1
            Dim parts = operatingLines(i).Split(","c)
            Dim CGID = parts(1)
            If Not operatingData.ContainsKey(CGID) Then operatingData(CGID) = parts
        Next

        ' 서버PC 데이터를 Dictionary로 로딩
        Dim serverData As New Dictionary(Of String, String())
        Dim serverLines = File.ReadAllLines(ServerFilePath)
        For i = 1 To serverLines.Length - 1
            Dim parts = serverLines(i).Split(","c)
            Dim CGID = parts(1)
            If Not serverData.ContainsKey(CGID) Then serverData(CGID) = parts
        Next

        ' CAM1, CAM2, CAM3 데이터 로딩 (같은 방식)
        Dim CAM1Data As New Dictionary(Of String, String())
        LoadCamData(CAM1FilePath, CAM1Data)
        Dim CAM2Data As New Dictionary(Of String, String())
        LoadCamData(CAM2FilePath, CAM2Data)
        Dim CAM3Data As New Dictionary(Of String, String())
        LoadCamData(CAM3FilePath, CAM3Data)

        ' 헤더 추출
        Dim OperatingHeaders As String() = File.ReadLines(OperatingFilePath).First().Split(","c)
        Dim ServerHeaders As String() = File.ReadLines(ServerFilePath).First().Split(","c)
        Dim CAM1Headers As String() = File.ReadLines(CAM1FilePath).First().Split(","c)

        ' Excel 객체 선언
        Dim excelApp As New Excel.Application
        Dim workbook As Excel.Workbook = excelApp.Workbooks.Add()
        Dim worksheet As Excel.Worksheet = CType(workbook.Sheets(1), Excel.Worksheet)

        ' 작업최적화
        excelApp.Visible = False ' 엑셀 보이지 않게 처리 (필요시 True)
        excelApp.ScreenUpdating = False
        excelApp.DisplayAlerts = False
        excelApp.Calculation = Excel.XlCalculation.xlCalculationManual

        ' 21번째 열의 너비를 14로 설정
        worksheet.Columns(21).ColumnWidth = 14

        ' 헤더작업
        Dim combinedHeader As New List(Of Object)
        combinedHeader.AddRange(OperatingHeaders)
        combinedHeader.Add("CropImage")
        combinedHeader.AddRange(ServerHeaders)
        combinedHeader.AddRange(CAM1Headers)
        For colIndex As Integer = 0 To combinedHeader.Count - 1
            worksheet.Cells(1, colIndex + 1) = combinedHeader(colIndex)
        Next

        TextBox_NumberOfOperation2.Text = operatingData.Count
        Dim rowNumber = 1
        For Each kvp In operatingData
            rowNumber += 1

            Dim fields = kvp.Value
            ' 필드 개별 변수 저장
            Dim PANEL_ID As String = fields(0)
            If PANEL_ID = "PANEL_ID" Then Continue For ' header continue
            Dim PALLET_ID As String = fields(1)
            Dim _LINE As String = fields(2)
            Dim INSPECT_TIME As String = fields(3)
            Dim JUDGE_AMI As String = fields(4)
            Dim JUDGE_INSP As String = fields(5)
            Dim PATTERN As String = fields(6)
            Dim DEFECT As String = fields(7)
            Dim AREA As String = fields(8)
            Dim IMG_PATH As String = fields(9)
            Dim MODEL As String = fields(10)
            Dim CORRECT_TT As String = fields(11)
            Dim VISION_TT As String = fields(12)
            Dim INSPECT_TT As String = fields(13)
            Dim CYCLE_TT As String = fields(14)
            Dim REPORT_TT As String = fields(15)
            Dim PALLET_TT As String = fields(16)
            Dim NG_COUNT As String = fields(17)
            Dim NG_CODE_AMI As String = fields(18)
            Dim NG_CODE_INSP As String = fields(19)

            ' 데이터 없으면 종료
            If PALLET_ID = "" Then
                Exit For
            End If

            ' 작업대상 그래픽 표기
            TextBox_NumberOfOperation.Text = rowNumber
            TextBox_PANEL_ID.Text = PANEL_ID
            TextBox_PALLET_ID.Text = PALLET_ID
            TextBox_LINE.Text = _LINE
            TextBox_INSPECT_TIME.Text = INSPECT_TIME
            TextBox_JUDGE_AMI.Text = JUDGE_AMI
            TextBox_JUDGE_INSP.Text = JUDGE_INSP
            TextBox_PATTERN.Text = PATTERN
            TextBox_DEFECT.Text = DEFECT
            TextBox_AREA.Text = AREA
            TextBox_IMG_PATH.Text = IMG_PATH
            TextBox_MODEL.Text = MODEL
            TextBox_CORRECT_TT.Text = CORRECT_TT
            TextBox_VISION_TT.Text = VISION_TT
            TextBox_INSPECT_TT.Text = INSPECT_TT
            TextBox_CYCLE_TT.Text = CYCLE_TT
            TextBox_REPORT_TT.Text = REPORT_TT
            TextBox_PALLET_TT.Text = PALLET_TT
            TextBox_NG_COUNT.Text = NG_COUNT
            TextBox_NG_CODE_AMI.Text = NG_CODE_AMI
            TextBox_NG_CODE_INSP.Text = NG_CODE_INSP

            ' 데이터 기록
            For i = 0 To fields.Length - 1
                worksheet.Cells(rowNumber, i + 1) = fields(i)
            Next
            worksheet.Rows(rowNumber).RowHeight = 90

            If JUDGE_AMI = "F" And rowNumber <> 1 Then
                ' 이미지 삽입
                If File.Exists(IMG_PATH) Then
                    InsertImage(IMG_PATH, worksheet, rowNumber)
                Else
                    Debug.WriteLine($"이미지 파일 없음: {IMG_PATH}")
                End If

                ' 서버PC 데이터 매칭 및 삽입
                If serverData.ContainsKey(PALLET_ID) Then
                    Dim ServerMatchedData() As String = serverData(PALLET_ID)
                    For i As Integer = 0 To ServerMatchedData.Length - 1
                        worksheet.Cells(rowNumber, OperatingHeaders.Length + 2 + i) = ServerMatchedData(i)
                    Next

                    Select Case ServerMatchedData(4) ' CAM_NO 값에 따라 CAM1, CAM2, CAM3 선택
                        Case "L"
                            InsertCamData(CAM1Data, PALLET_ID, rowNumber, OperatingHeaders, ServerHeaders, worksheet)
                        Case "C"
                            InsertCamData(CAM2Data, PALLET_ID, rowNumber, OperatingHeaders, ServerHeaders, worksheet)
                        Case "R"
                            InsertCamData(CAM3Data, PALLET_ID, rowNumber, OperatingHeaders, ServerHeaders, worksheet)
                    End Select

                Else
                    Debug.WriteLine($"서버 데이터 없음: {PALLET_ID}")
                End If


            End If

        Next

        excelApp.Calculation = Excel.XlCalculation.xlCalculationAutomatic
        excelApp.ScreenUpdating = True

        ' 저장 경로
        Dim savePath As String = Path.ChangeExtension(OperatingFilePath, ".xlsx")
        workbook.SaveAs(savePath)
        workbook.Close()
        excelApp.Quit()

        ' 리소스 해제
        ReleaseObject(worksheet)
        ReleaseObject(workbook)
        ReleaseObject(excelApp)

        MessageBox.Show("Excel 파일 저장 완료: " & savePath, "완료", MessageBoxButtons.OK, MessageBoxIcon.Information)

    End Sub

    Private Sub Button_운영PC_Click(sender As Object, e As EventArgs) Handles Button_운영PC.Click
        Using ofd As New OpenFileDialog()
            ofd.Filter = "Text Files|*.txt"
            ofd.Title = "운영PC 파일 선택"

            If ofd.ShowDialog() = DialogResult.OK Then
                TextBox_운영PC.Text = ofd.FileName
            End If
        End Using
    End Sub

    Private Sub Button_서버PC_Click(sender As Object, e As EventArgs) Handles Button_서버PC.Click
        Using ofd As New OpenFileDialog()
            ofd.Filter = "CSV Files|*.csv|All Files|*.*"
            ofd.Title = "서버PC 파일 선택"

            If ofd.ShowDialog() = DialogResult.OK Then
                TextBox_서버PC.Text = ofd.FileName
            End If
        End Using
    End Sub

    Private Sub Button_CAM1_Click(sender As Object, e As EventArgs) Handles Button_CAM1.Click
        Using ofd As New OpenFileDialog()
            ofd.Filter = "CSV Files|*.csv|All Files|*.*"
            ofd.Title = "CAM1 파일 선택"

            If ofd.ShowDialog() = DialogResult.OK Then
                TextBox_CAM1.Text = ofd.FileName
            End If
        End Using
    End Sub

    Private Sub Button_CAM2_Click(sender As Object, e As EventArgs) Handles Button_CAM2.Click
        Using ofd As New OpenFileDialog()
            ofd.Filter = "CSV Files|*.csv|All Files|*.*"
            ofd.Title = "CAM2 파일 선택"

            If ofd.ShowDialog() = DialogResult.OK Then
                TextBox_CAM2.Text = ofd.FileName
            End If
        End Using
    End Sub

    Private Sub Button_CAM3_Click(sender As Object, e As EventArgs) Handles Button_CAM3.Click
        Using ofd As New OpenFileDialog()
            ofd.Filter = "CSV Files|*.csv|All Files|*.*"
            ofd.Title = "CAM3 파일 선택"

            If ofd.ShowDialog() = DialogResult.OK Then
                TextBox_CAM3.Text = ofd.FileName
            End If
        End Using
    End Sub

    ' CAM 데이터 삽입 함수
    Private Sub InsertCamData(camData As Dictionary(Of String, String()), palletID As String, rowNumber As Integer, operatingHeaders As String(), serverHeaders As String(), worksheet As Excel.Worksheet)
        If camData.ContainsKey(palletID) Then
            Dim matchedData() As String = camData(palletID)
            For i As Integer = 0 To matchedData.Length - 1
                worksheet.Cells(rowNumber, operatingHeaders.Length + 2 + serverHeaders.Length + i) = matchedData(i)
            Next
        End If
    End Sub

End Class

```

끝.