> ตรวจดูการทำงานของเว็บไซต์ เพื่อนำไปสู่คำตอบสุดท้าย
>
> https://inspect.sspk.xyz
>
> หมายเหตุ รูปแบบของ Flag ที่เป็นคำตอบของข้อนี้คือ flag{…}
>

![image](https://github.com/user-attachments/assets/a584b9a6-17e8-49bf-a778-774aa4937ed0)

เปิดมาเป็นหน้าเว็บโง่ๆเปล่าๆ ดูไม่มีอะไร แต่ดูจากชื่อ subdomain มีคำว่า inspect ขอเดาไว้ก่อนว่าต้องไปทำอะไรซักอย่างกับ web inspect
เปิดดู page source ก็จะพบ JavaScript function ที่เกี่ยวกับการเข้ารหัส flag อยู่

![image](https://github.com/user-attachments/assets/baf00a08-7577-4aca-906c-512b444832d0)

ตัว script จะมีการเรียก flag ไปที่ `/flag.php` และเช็คค่า `msg` ที่ได้รับ หากค่าตรงกับ “give me flag” จะ return ค่า flag กลับมา ว่าแล้วก็ลองใช้ repeater แล้วเรียกเส้น `/flag.php?msq=give+me+flag`

![image](https://github.com/user-attachments/assets/4fd3c195-30a2-411a-9d94-4c8656c6eb3e)

ได้เป็นข้อความแปลกๆออกมา พบว่าโดนเข้ารหัสแบบ XOR เอาไว้ เขียนโค้ดแกะออกมาก็จะได้ flag

```python
# Given encoded text
encoded_text = "îäéïó¹æûø»ë¿×Íä»å»æü×º¿°¾°°¼¹¾¿õ"

# Decoding logic: XOR each character's ASCII value with 0x88
decoded_text = ''.join(chr(ord(char) ^ 0x88) for char in encoded_text)
decoded_text
```

![image](https://github.com/user-attachments/assets/c56f3ee8-8dcb-4cf8-be59-984638b22945)
