> ทางตำรวจปราบปรามแฮกเกอร์ได้ ยึดคอมพิวเตอร์จากผู้ต้องสงสัยคนหนึ่งมา และในคอมพิวเตอร์เครื่องนั้นมีการติดตั้ง Web Browser อันหนึ่งที่คาดว่าคนร้าย จะใช้ในการทำผิดกฎหมาย คุณในฐานะผู้เชี่ยวชาญด้าน CyberSecurity ช่วยตรวจสอบหลักฐานชิ้นนี้ให้หน่อยว่าคนร้ายมีความพยายามจะค้นหาข้อมูลอะไร?
> 
> 
> URL ดาวน์โหลดโจทย์: https://static.wtctt24.p7z.pw/open_for1.zip
> 

ข้อนี้ว่าด้วยการตามหา flag จาก Firefox History แตกไฟล์ออกมา จะได้ออกมาหน้าตาแบบนี้

![image](https://github.com/user-attachments/assets/6c7f2b73-ed18-418d-a56e-8b756e66a1f8)

จากการไป research แบบรีบๆมาจะพบว่า ใน Firefox เวลาเราไปเข้าเว็บต่างๆ ประวัติจะถูกเก็บไว้ในไฟล์ places.sqlite ก็เลยเอา SQLite เปิดขึ้นมาดู

![image](https://github.com/user-attachments/assets/95f13fd1-4c49-4837-ad29-e05c8391343a)

กดไปเรื่อยๆ จนมาเจอ table `moz_places` จะเห็นว่ามีการใช้งาน google เยอะมาก แต่ถ้าสังเกตดีๆก็จะเจอว่าเขา search google ด้วยตัวอักษรทีละตัวหมดเลย ซึ่งถ้าเอามาประกอบร่างรวมกันแล้ว นี่มัน flag ชัดๆ
ทีนี้ครั้นจะแกะ flag ทีละตัวก็เสียเวลา เลยเขียน query ให้มันเอา flag ออกมาให้เราทีเดียวเลยดีกว่า

```sql
SELECT group_concat(substr(title, 1, 1), '') AS flag FROM moz_places WHERE title LIKE '%ค้นหาด้วย google%';
```

![image](https://github.com/user-attachments/assets/703aae69-8210-4ba1-9fbd-cadf91cc1550)
