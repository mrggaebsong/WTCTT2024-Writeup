> ปีนี้คอมพิวเตอร์ของเสี่ย mirthz โดนมัลแวร์ทำให้ไฟล์พนักงานลูกค้าและสินค้าถูกเข้ารหัสจนไม่สามารถเปิดไฟล์ได้ 
> คุณผู้ซึ่งเป็นลีดเดอร์ของทีม forensic ขององค์กร ช่วยตรวจสอบให้หน่อยนะ
>
> ข้อควรระวัง **โปรดทำโจทย์เหล่านี้ใน VM เพื่อความปลอดภัย เนื่องจากโจทย์เหล่านี้มีความเกี่ยวข้องกับมัลแวร์**
>
> Part 2 : เหตุการณ์นี้เกิดจากการที่มีไฟล์มัลแวร์ได้เข้ามาในเครื่องคอมพิวเตอร์ของเสี่ย mirthz
> 
> ซึ่งตอนนี้ทีม forensic ขององค์กรได้เก็บไฟล์ดังกล่าวไว้แล้ว ช่วยตรวจสอบหน่อยว่าไฟล์มัลแวร์มาจากไหนและชื่อไฟล์อะไร
>
> Format flag Part 2 : flag{IP:PORT,file.exe} เช่น flag{10.10.34.56:1234,ransomware.exe}
> 

Just recursive grep “.exe” and we will get the payload in ConsoleHost_history

![image](https://github.com/user-attachments/assets/a123fef6-ae9a-4841-897c-c689936a813b)

FLAG: flag{10.211.55.2:888,wowza.exe}
