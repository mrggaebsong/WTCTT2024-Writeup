แตกไฟล์ zip ออกมา จะได้เป็นไฟล์ MyHomework.docm อันเป็นไฟล์ template ที่มีการฝัง macro ซึ่งอย่าหาเปิดบนเครื่อง host เพราะมันจะ spawn tab youtube จน chrome คุณระเบิด! ผมเตือนแล้วนะ
ก่อนอื่น เรา copy file .docm ออกมาเป็นไฟล์ zip: `cp MyHomework.docm MyHomework.zip` แล้วแตกไฟล์ zip ออกมา

![image](https://github.com/user-attachments/assets/bf3c5171-05c0-4513-92d9-785b923d3444)

จะได้ออกมาเป็น folder word และไฟล์ vbaProjects.bin ที่เราจะเอามาวิเคราะห์ตัว macro กัน ซึ่งเราจะใช้ tool olevba ในการ decompile vba.

```bash
pip install oletools
olevba vbaProject.bin
```

![image](https://github.com/user-attachments/assets/e55ac368-bdad-465e-ba19-e9decd8ada1f)

```vba
Sub Document_Open()
    Dim Z1q8 As String, G3l2 As String, Y5f4 As String, W2x7 As String
    Dim N9b6 As String, H4m3 As String, P7z5 As String

    Z1q8 = Chr(102) & Chr(108) & Chr(97) & Chr(102 + 1)
    G3l2 = Chr(123) & Chr(118) & Chr(97 + 1) & Chr(97) & Chr(95) & Chr(115) & Chr(96 + 3) & Chr(114) & Chr(105) & Chr(112) & Chr(116) & Chr(105) & Chr(110) & Chr(103)
    Y5f4 = Chr(95) & Chr(105) & Chr(115) & Chr(95)
    W2x7 = Chr(110) & Chr(111) & Chr(116) & Chr(95) & Chr(114) & Chr(101) & Chr(97) & Chr(108) & Chr(108) & Chr(121)
    N9b6 = Chr(95) & Chr(116) & Chr(104) & Chr(97) & Chr(116) & Chr(95) & Chr(104) & Chr(97) & Chr(114) & Chr(100)
    H4m3 = Chr(95) & Chr(101) & Chr(104) & Chr(95) & Chr(54) & Chr(56) & Chr(53) & Chr(51) & Chr(50) & Chr(53) & Chr(52) & Chr(55) & Chr(53) & Chr(50)
    P7z5 = Chr(125)

    Dim fullSecret As String
    fullSecret = Z1q8 & G3l2 & Y5f4 & W2x7 & N9b6 & H4m3 & P7z5

    Dim Y8p3 As String, B5w1 As String, F9l3 As String, N3t7 As String
    Y8p3 = "h" & "t" & "t" & "p" & Chr(58) & Chr(47) & Chr(47)
    B5w1 = "w" & "w" & "w" & Chr(46) & "y"
    F9l3 = "o" & Chr(117) & "t" & "u" & "b" & "e" & Chr(46)
    N3t7 = "c" & "o" & "m" & Chr(47) & "w" & "a" & "t" & "c" & "h" & Chr(63) & "v" & Chr(61) & "hmDAeL1I8Lo"

    Dim m8Pq As String
    m8Pq = Y8p3 & B5w1 & F9l3 & N3t7

    Dim shell
    Set shell = CreateObject("WScript.Shell")

    For i = 1 To 78
        shell.Run m8Pq
    Next i


End Sub
```

แกะ VB script ออกมาได้แล้ว แต่ว่าโค้ดโดน obfuscate ไว้อยู่ด้วยการแทนตัวอักษรต่างๆด้วย `Char(ASCII)` ซึ่งถ้านำมาประกอบรวมร่างกันก็จะออกมาเป็น flag ในตัวแปร `fullSecret`

```vba
Z1q8 = Chr(102) & Chr(108) & Chr(97) & Chr(102 + 1) 
' Z1q8 = "flag"

G3l2 = Chr(123) & Chr(118) & Chr(97 + 1) & Chr(97) & Chr(95) & Chr(115) & Chr(96 + 3) & Chr(114) & Chr(105) & Chr(112) & Chr(116) & Chr(105) & Chr(110) & Chr(103)
' G3l2 = "{vba_scripting"

Y5f4 = Chr(95) & Chr(105) & Chr(115) & Chr(95)
' Y5f4 = "_is_"

W2x7 = Chr(110) & Chr(111) & Chr(116) & Chr(95) & Chr(114) & Chr(101) & Chr(97) & Chr(108) & Chr(108) & Chr(121)
' W2x7 = "not_really"

N9b6 = Chr(95) & Chr(116) & Chr(104) & Chr(97) & Chr(116) & Chr(95) & Chr(104) & Chr(97) & Chr(114) & Chr(100)
' N9b6 = "_that_hard"

H4m3 = Chr(95) & Chr(101) & Chr(104) & Chr(95) & Chr(54) & Chr(56) & Chr(53) & Chr(51) & Chr(50) & Chr(53) & Chr(52) & Chr(55) & Chr(53) & Chr(50)
' H4m3 = "_eh_6853254752"

P7z5 = Chr(125)
' P7z5 = "}"

fullSecret = Z1q8 & G3l2 & Y5f4 & W2x7 & N9b6 & H4m3 & P7z5
' fullSecret = "flag{vba_scripting_is_not_really_that_hard_eh_6853254752}"
```
