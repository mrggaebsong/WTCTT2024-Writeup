โยน binary file เข้าไปใน IDA Free ของเรา มองไปทางซ้าย จะมี function `secretFunction()` อยู่ จัดการ decompile มันออกมาแล้วจะได้ psuedocode ออกมาชุดหนึ่ง

![image](https://github.com/user-attachments/assets/d93bb128-44ef-4d4f-8bf5-ce4f48cf79b7)

```c
int secretFunction()
{
  _QWORD v1[5]; // [rsp+0h] [rbp-30h] BYREF
  unsigned int i; // [rsp+2Ch] [rbp-4h]

  qmemcpy(v1, "jmkwh4n?8584ii9j>4o=;ji9i94oh>?=m49=q", 38);
  for ( i = 0; i <= 0x25; ++i )
    putchar(*((_BYTE *)v1 + (int)i) ^ 0xC);
  return putchar(10);
}
```

เราจะพบว่ามีการใช้ loop ที่คาดว่าน่าจะเป็นการเข้ารหัสข้อความที่ดูแล้วน่าจะเป็น flag และทุกตัวอักษรมีการทำ XOR เอาไว้ reverse code เพื่อเอา flag ออกมา

```python
# The encoded string from the C code
encoded_string = "j`mkwh4n?8584ii9j>4o=;ji9i94oh>?=m49=q"

# Decoding process using XOR with 0xC
decoded_flag = "".join(chr(ord(char) ^ 0xC) for char in encoded_string)

decoded_flag
```

![image](https://github.com/user-attachments/assets/14ccdf6c-d288-471d-9067-864947ba7c82)
