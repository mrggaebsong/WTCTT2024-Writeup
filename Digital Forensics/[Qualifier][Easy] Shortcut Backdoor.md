> ในช่วงที่ทาง ThaiCERT ได้แจ้งเตือน การโจมตีด้วยไฟล์ Shortcut ของ Windows ที่ฝังมัลแวร์ คุณได้พบว่า คอมพิวเตอร์ที่ทำงานของคุณมีไฟล์ Windows Shortcut ประหลาดโผล่ขึ้นมาบน Desktop ไหนลองตรวจสอบดูหน่อย ว่าไฟล์นี้มีโค้ดอะไรแปลก ๆ ฝังไว้หรือเปล่า?
> 
> URL ดาวน์โหลดโจทย์: https://static.wtctt24.p7z.pw/open_for2.zip
>

แตกไฟล์ออกมา ได้เป็นไฟล์ shortcut เลยเอาไป exiftool เจอเป็น Command PowerShell ที่มี encode Base64. decode ออกมาปึ้ง จบๆ

![image](https://github.com/user-attachments/assets/dca86a27-bb72-4dd9-9642-ea50207a856b)
