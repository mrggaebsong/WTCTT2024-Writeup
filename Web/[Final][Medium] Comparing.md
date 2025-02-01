> การเข้า Hash แบบไม่ถูกต้องจะไม่ปลอดภัยอีกต่อไป
> 
> 
> https://comparing.sspk.xyz
> 
> หมายเหตุ รูปแบบของ Flag ที่เป็นคำตอบของข้อนี้คือ flag{…}
>

![image](https://github.com/user-attachments/assets/d41ab499-6e80-448c-b484-2b6e5812169f)

WTF is that!? ไหนลองมาดูโค้ดซิ

```php
<?php
    $flag = (file_get_contents("../flag.txt"));
    if(isset($_GET['md4'])){
        if ($_GET["md4"] == hash("md4", $_GET["md4"])){
                echo $flag;
            }
            else{
                echo nl2br("Part 1/2: flag{Php_1s\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\nHint: I like PHP comparing str.");
            }
    }
    else {
        show_source(__FILE__);
    }
    
?>
```

จะเห็นได้ว่าตัวโจทย์จะทำการดึงค่า flag ออกมา เมื่อค่า hash MD4 ที่ได้รับมาตรงกับค่าที่กำหนดไว้ แต่ถ้ามาดูบรรทัดการเทียบค่าดีๆ

```php
if ($_GET["md4"] == hash("md4", $_GET["md4"]))
```

ในขั้นตอนการเปรียบเทียบจะใช้เครื่องหมาย `==` แทน `===` ซึ่งเรียกว่า “PHP loose comparison vulnerability” ทำให้ PHP จะเปรียบเทียบค่าหลังจากแปลงเป็นตัวเลข (ต่างจาก `===` ที่เทียบทั้งค่า strings value และ file type) 
เราสามารถ bypass comparing นี้ด้วยวิธีการทำ “PHP Juggling” ได้ โดยค่า hash ที่ว่าต้องขึ้นต้นด้วย `0e` และตามด้วย strings เป็นตัวเลขเท่านั้น จึงจะหลอกให้ PHP แปลงค่า strings ที่ตามหลังทั้งหมดให้เป็นตัวเลขจริงๆ
ทีนี้ลองมาใส่ค่า random เพื่อดูว่า ถ้าค่าไม่ถูกต้อง เราจะได้แค่ flag ส่วนแรกเท่านั้น

![image](https://github.com/user-attachments/assets/d371a329-686d-415c-ac76-2061c0b01e7d)

พอได้แบบนี้แล้วก็มาเขียน script เพื่อหา hash collision มาดูว่า MD4 ตัวไหนที่เทียบกันแล้วได้ค่าไม่เท่ากัน

```python
import hashlib
import re

prefix = "0e"
iters = 0

while True:
    candidate = prefix + str(iters)  # Example: "0e12345"
    md4_hash = hashlib.new('md4', candidate.encode()).hexdigest()  # Calculate MD4 hash

    if re.match(r"^0e\d{30}$", md4_hash):  # Check if it follows the "0e..." pattern
        print(f"[+] Found! md4({candidate}) ---> {md4_hash}")
        print(f"[+] Success after {iters} iterations")
        break  # Stop when we find a valid hash

    iters += 1

    if iters % 1000000 == 0:
        print(f"[+] Current attempt: {candidate}, Iterations: {iters}, Still searching...")
```

ปล. เนื่องจากต้องวนลูป brute force เพื่อหาค่าที่ถูกต้อง ต้องทำใจและรอเวลามันซักพักใหญ่ๆ

![image](https://github.com/user-attachments/assets/ca912abe-62ef-464d-a569-2c1b2d6ccffe)

พอได้ค่าแล้วก็เอา MD4 hash ไปใส่ใน `/?md4=` ต่อท้าย URL เพื่อดึงค่า flag ออกมา

![image](https://github.com/user-attachments/assets/e4f6c862-7f89-4d9a-be85-42aceea59936)
