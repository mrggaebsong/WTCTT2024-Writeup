> บริษัท Threat Intelligence แจ้งมาว่า แฮกเกอร์ ที่เจาะระบบหน่วยงานเราได้ซ่อน Virtual Host (vHost) ของ C2 แอบไว้ในเว็บเซิร์ฟเวอร์คุณใน ฐานะ Red Teamer จงเข้าไปหา vHost ของแฮกเกอร์ออกมา
> 
> 
> URL เว็บโจทย์: [http://wtctt24.open.web1.p7z.pw](http://wtctt24.open.web1.p7z.pw/)
> 
> หมายเหตุ:
> 
> - vHost ซ่อนอยู่ที่ http://<vHost>.wttctt24.open.web1.p7z.pw
> - HTTP Port 80 (ไม่ใช่ HTTPS ต้องเข้าด้วย http:// เท่านั้น)
> - ใช้ Wordlist จาก https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/deepmagic.com-prefixes-top500.txt
> - Flag จะอยู่ในรูปแบบ flag{open_webX_XXX} เช่น flag{open_web1_e758148af358200bf364c8d9282b7f04}
> - ช่องโหว่ไม่จำเป็นต้องทำ Port Scan ด้วย Nmap
> - ช่องโหว่ไม่จำเป็นต้องทำ URL Fuzzing เพื่อหา Directory หรือ File ที่เป็นชื่อลับ
> - โจทย์ไม่ได้ถูกออกแบบมาให้แก้ไข โดยใช้โปรแกรมสแกนช่องโหว่อัตโนมัติ

![image](https://github.com/user-attachments/assets/c7c13333-6693-45c3-93b0-164bc3c99292)

Browse หน้าเว็บขึ้นมา ก็ไปดู page source ก่อนเป็นที่แรก เลื่อนลงไปเจอ hint ปั้ง ถ้าใครไม่รู้จักการ fuzz vhost ก็ถือว่าเป็นตัวช่วยที่ดีมากๆ

![image](https://github.com/user-attachments/assets/ab95c38b-dbb9-47c2-8ec7-4b44367919ff)

ต้องทำการ fuzz vhost ไปที่ `FUZZ.wtctt24.open.web1.p7z.pw` และทางโจทย์ก็ได้กำหนดให้ใช้ deepmagic.com-prefixes-top500.txt เป็น wordlist ในการ fuzz ซึ่งเราจะใช้ `ffuf` ในการซัดกระบวนท่า
`ffuf -H "Host: FUZZ.wtctt24.open.web1.p7z.pw" -c -w "deepmagic.com-prefixes-top500.txt" -u http://wtctt24.open.web1.p7z.pw/`

![image](https://github.com/user-attachments/assets/60d1b231-25c5-481c-8001-a59ac3977b5c)

fuzz ไปปั๊บก็จะเจอ vhost ที่ hidden-host เปิด burp ขึ้นมา browse web ShadowNet แล้วเปลี่ยน Host header เป็น hidden host ที่เราเจอ

![image](https://github.com/user-attachments/assets/27240607-ea49-405d-ae4d-1fdca4b7afaa)
