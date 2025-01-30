> ปีนี้คอมพิวเตอร์ของเสี่ย mirthz โดนมัลแวร์ทำให้ไฟล์พนักงานลูกค้าและสินค้าถูกเข้ารหัสจนไม่สามารถเปิดไฟล์ได้ 
> คุณผู้ซึ่งเป็นลีดเดอร์ของทีม forensic ขององค์กร ช่วยตรวจสอบให้หน่อยนะ
>
> ข้อควรระวัง **โปรดทำโจทย์เหล่านี้ใน VM เพื่อความปลอดภัย เนื่องจากโจทย์เหล่านี้มีความเกี่ยวข้องกับมัลแวร์**
>
> Part 1 : ช่วยตรวจสอบหน่อยว่าไฟล์ที่ถูกเข้ารหัสดังกล่าวมีนามสกุลไฟล์ว่าอะไร
>
> Format flag Part 1 : flag{.extension}  เช่น flag{.ransomware}
>
> Challenge File : https://bit.ly/3WmS7I0
> Alternative link : https://bit.ly/42gt8tw
>
> unzip password: infected
> 

แตกไฟล์ zip ออกมา จะได้ folder เป็น “Victim_Triage” เปิดเข้าไปดูจะเจอกับ drive ของเหยื่อคนหนึ่งที่ถูก ransomware เล่นงาน

![image](https://github.com/user-attachments/assets/e04d4b50-c08d-4909-8129-b75e254d042e)

ส่องไปที่ Mirth’s Desktop จะพบไฟล์มากมายที่ถูกเข้ารหัสจาก ransomware ด้วย extension “.wowza” นี่แหละคือคำตอบ

FLAG: flag{.wowza}
