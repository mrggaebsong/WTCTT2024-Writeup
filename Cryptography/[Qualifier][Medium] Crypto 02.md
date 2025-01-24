แตกไฟล์ได้เป็น 2 ไฟล์ เป็นรูปหมูเด้งเจนเอไอหนึ่ง และไฟล์ text ที่ล้อมาจากรูปหมูเด้งอีกหนึ่ง

![image](https://github.com/user-attachments/assets/13ca8f2f-c13b-4f96-a3c8-dbbe2194a106)

ตอนแรกงมตั้งนาน นึกว่าไอไฟล์ text นี่จะมีอะไรสัมพันธ์กับรูปหมูเด้งนี่รึเปล่า สรุป แค่เลื่อนไฟล์ text ลงไปล่างสุด จะเจอบรรทัดรองสุดท้ายที่เหมือนเป็น ciphertext อะไรสักอย่าง

![image](https://github.com/user-attachments/assets/43c08027-ed7f-455a-a559-01467cac5e19)

แต่ทุกตัวอักษรมี # คั่นไว้หมดเลยเอาพวกนังออกไปซะ และจากการเอาไปเข้า cipher identifier จะได้ออกมาเป็น Base62 encoding

![image](https://github.com/user-attachments/assets/df9551fd-ac34-428c-b9d1-30e35698440e)

ยังจะมีซ้อนอีกชั้น ซึ่งดูออกเลยว่า Caesar Cipher ของแทร่แน่นอน ก็จัดไปเลยจ้า หมุน shift ไปเรื่อยๆ จนกระทั่ง flag ออกที่ shift 13 (หรือจริงๆ เอาเข้า CyberChef แล้วถอดด้วย ROT13 ก็เป็นอันจบ)

![image](https://github.com/user-attachments/assets/ea54d966-d6db-4212-9d86-fb43d9e3cbdc)
