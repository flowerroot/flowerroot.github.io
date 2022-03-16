---
layout: single
title: "Vending Machine."
categories: Visual-Basic
tag: [Visual-Basic]
toc: true
toc_sticky: true
---

친구의 두 번째 과제..  
Visual Basic으로 만든 자판기입니다.  

## 소스코드  
```vb
Public Class Form1
    Dim total As Integer
    Dim sel As Integer
    Dim money As Integer
    Dim cell As Object
    Dim cell_file As Object
    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles MyBase.Load
        cell = CreateObject("excel.application")
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")
        Button1.Text = cell_file.sheets(1).cells(1, 1).value
        Button2.Text = cell_file.sheets(1).cells(8, 1).value
        Button3.Text = cell_file.sheets(1).cells(15, 1).value
        cell_file.close()
    End Sub

    Private Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")
        Label1.Text = cell_file.sheets(1).cells(2, 1).value
        Label2.Text = cell_file.sheets(1).cells(3, 1).value
        Label3.Text = cell_file.sheets(1).cells(4, 1).value
        Label4.Text = cell_file.sheets(1).cells(5, 1).value
        Label5.Text = cell_file.sheets(1).cells(6, 1).value

        Label6.Text = cell_file.sheets(1).cells(2, 2).value
        Label7.Text = cell_file.sheets(1).cells(3, 2).value
        Label8.Text = cell_file.sheets(1).cells(4, 2).value
        Label9.Text = cell_file.sheets(1).cells(5, 2).value
        Label10.Text = cell_file.sheets(1).cells(6, 2).value
        cell_file.close()

        PictureBox1.Image = My.Resources.오리지널
        PictureBox2.Image = My.Resources.카라멜
        PictureBox3.Image = My.Resources.치츠팝콘
        PictureBox4.Image = My.Resources.치토스팝콘
        PictureBox5.Image = My.Resources.반반

        sel = 1
    End Sub

    Private Sub Button2_Click(sender As Object, e As EventArgs) Handles Button2.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")
        Label1.Text = cell_file.sheets(1).cells(9, 1).value
        Label2.Text = cell_file.sheets(1).cells(10, 1).value
        Label3.Text = cell_file.sheets(1).cells(11, 1).value
        Label4.Text = cell_file.sheets(1).cells(12, 1).value
        Label5.Text = cell_file.sheets(1).cells(13, 1).value

        Label6.Text = cell_file.sheets(1).cells(9, 2).value
        Label7.Text = cell_file.sheets(1).cells(10, 2).value
        Label8.Text = cell_file.sheets(1).cells(11, 2).value
        Label9.Text = cell_file.sheets(1).cells(12, 2).value
        Label10.Text = cell_file.sheets(1).cells(13, 2).value
        cell_file.close()

        PictureBox1.Image = My.Resources.콜라
        PictureBox2.Image = My.Resources.사이다
        PictureBox3.Image = My.Resources.아메리카노
        PictureBox4.Image = My.Resources.오랜지주스
        PictureBox5.Image = My.Resources.아이스티

        sel = 2
    End Sub

    Private Sub Button3_Click(sender As Object, e As EventArgs) Handles Button3.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")
        Label1.Text = cell_file.sheets(1).cells(16, 1).value
        Label2.Text = cell_file.sheets(1).cells(17, 1).value
        Label3.Text = cell_file.sheets(1).cells(18, 1).value
        Label4.Text = Nothing
        Label5.Text = Nothing

        Label6.Text = cell_file.sheets(1).cells(16, 2).value
        Label7.Text = cell_file.sheets(1).cells(17, 2).value
        Label8.Text = cell_file.sheets(1).cells(18, 2).value
        Label9.Text = Nothing
        Label10.Text = Nothing
        cell_file.close()

        PictureBox1.Image = My.Resources.칠리치즈나쵸
        PictureBox2.Image = My.Resources.피자
        PictureBox3.Image = My.Resources.크림치츠
        PictureBox4.Image = Nothing
        PictureBox5.Image = Nothing

        sel = 3
    End Sub

    Private Sub Button5_Click(sender As Object, e As EventArgs) Handles Button5.Click
        money += 1000
        TextBox1.Text = money
    End Sub

    Private Sub TextBox1_TextChanged(sender As Object, e As EventArgs) Handles TextBox1.TextChanged

    End Sub

    Private Sub TextBox2_TextChanged(sender As Object, e As EventArgs) Handles TextBox2.TextChanged

    End Sub

    Private Sub Button6_Click(sender As Object, e As EventArgs) Handles Button6.Click
        money += 5000
        TextBox1.Text = money
    End Sub

    Private Sub Button7_Click(sender As Object, e As EventArgs) Handles Button7.Click
        money += 10000
        TextBox1.Text = money
    End Sub

    Private Sub PictureBox1_Click(sender As Object, e As EventArgs) Handles PictureBox1.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")

        If sel = 1 Then
            If money >= cell_file.sheets(1).cells(2, 2).value Then
                money -= cell_file.sheets(1).cells(2, 2).value
                total += cell_file.sheets(1).cells(2, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 2 Then
            If money >= cell_file.sheets(1).cells(9, 2).value Then
                money -= cell_file.sheets(1).cells(9, 2).value
                total += cell_file.sheets(1).cells(9, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 3 Then
            If money >= cell_file.sheets(1).cells(16, 2).value Then
                money -= cell_file.sheets(1).cells(16, 2).value
                total += cell_file.sheets(1).cells(16, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        End If

        cell_file.close()
    End Sub

    Private Sub Button4_Click(sender As Object, e As EventArgs) Handles Button4.Click
        MsgBox("구매가 완료되었습니다. 거스름돈: " & money)
        money = 0
        total = 0
        TextBox1.Text = Nothing
        TextBox2.Text = Nothing
        TextBox3.Text = Nothing
    End Sub

    Private Sub PictureBox2_Click(sender As Object, e As EventArgs) Handles PictureBox2.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")

        If sel = 1 Then
            If money >= cell_file.sheets(1).cells(3, 2).value Then
                money -= cell_file.sheets(1).cells(3, 2).value
                total += cell_file.sheets(1).cells(3, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 2 Then
            If money >= cell_file.sheets(1).cells(10, 2).value Then
                money -= cell_file.sheets(1).cells(10, 2).value
                total += cell_file.sheets(1).cells(10, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 3 Then
            If money >= cell_file.sheets(1).cells(17, 2).value Then
                money -= cell_file.sheets(1).cells(17, 2).value
                total += cell_file.sheets(1).cells(17, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        End If

        cell_file.close()
    End Sub

    Private Sub PictureBox3_Click(sender As Object, e As EventArgs) Handles PictureBox3.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")

        If sel = 1 Then
            If money >= cell_file.sheets(1).cells(4, 2).value Then
                money -= cell_file.sheets(1).cells(4, 2).value
                total += cell_file.sheets(1).cells(4, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 2 Then
            If money >= cell_file.sheets(1).cells(11, 2).value Then
                money -= cell_file.sheets(1).cells(11, 2).value
                total += cell_file.sheets(1).cells(11, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 3 Then
            If money >= cell_file.sheets(1).cells(18, 2).value Then
                money -= cell_file.sheets(1).cells(18, 2).value
                total += cell_file.sheets(1).cells(18, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        End If

        cell_file.close()
    End Sub

    Private Sub PictureBox4_Click(sender As Object, e As EventArgs) Handles PictureBox4.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")

        If sel = 1 Then
            If money >= cell_file.sheets(1).cells(5, 2).value Then
                money -= cell_file.sheets(1).cells(5, 2).value
                total += cell_file.sheets(1).cells(5, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 2 Then
            If money >= cell_file.sheets(1).cells(12, 2).value Then
                money -= cell_file.sheets(1).cells(12, 2).value
                total += cell_file.sheets(1).cells(12, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        End If

        cell_file.close()
    End Sub

    Private Sub PictureBox5_Click(sender As Object, e As EventArgs) Handles PictureBox5.Click
        cell_file = cell.workbooks.open("D:\수업자료\건축디자인학과 김규완 기말과제\건축디자인학과 김규완 기말과제\팝콘 가격.xlsx")

        If sel = 1 Then
            If money >= cell_file.sheets(1).cells(6, 2).value Then
                money -= cell_file.sheets(1).cells(6, 2).value
                total += cell_file.sheets(1).cells(6, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        ElseIf sel = 2 Then
            If money >= cell_file.sheets(1).cells(13, 2).value Then
                money -= cell_file.sheets(1).cells(13, 2).value
                total += cell_file.sheets(1).cells(13, 2).value
                TextBox2.Text = total
                TextBox3.Text = money
            Else
                MsgBox("금액이 부족합니다.")
            End If
        End If

        cell_file.close()
    End Sub
End Class

```

## 실행화면  

![Form1 2022-03-16 오후 10_14_51](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_14_51.png)  
처음 실행 화면  

![Form1 2022-03-16 오후 10_14_54](../../images/ 2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_14_54.png)  
팝콘을 선택하면 팝콘 종류가 나오고  

![Form1 2022-03-16 오후 10_14_57](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_14_57.png)  
음료를 선택하면 음료 종류  

![Form1 2022-03-16 오후 10_15_00](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_00.png)  
스낵을 선택하면 스낵 종류  

![Form1 2022-03-16 오후 10_15_06](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_06.png)  
금액을 투입하면 투입된 금액이 표시된다.  

![Form1 2022-03-16 오후 10_15_11](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_11.png)  
크림치즈 프레즐을 선택하면 선택상품 총액에 4000원이 추가되고 거스름돈이 계산된다.  

![Form1 2022-03-16 오후 10_15_16](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_16.png)  
팝콘에서 카라멜을 선택하면 선택상품 총액이 9500원으로 증가하고 다시 거스름돈이 계산된다.  

![Form1 2022-03-16 오후 10_15_22](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_22.png)  
구입을 클릭하면 메세지박스가 나온다.  

![Form1 2022-03-16 오후 10_15_25](../../images/2022-03-16-VB-Vending-Machine/Form1 2022-03-16 오후 10_15_25.png)  
다시 초기 화면으로 돌아온다.  