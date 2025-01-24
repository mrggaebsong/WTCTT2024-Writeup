ได้โค้ดออกมา ถึงหน้าตาจะดูแปลกประหลาดเพราะตั้งชื่อตัวแปรให้ดูอ่านยากเฉยๆ แต่จริงๆแล้วก็คือเอา hex input มาแกะ

```python
import sys

def d3crypt_m3ss4ge(h3x_1nput, k3y):
    k3y = k3y % 256
    byt3_d4t4 = bytes.fromhex(h3x_1nput)
    d3crypt3d = bytes(b ^ k3y for b in byt3_d4t4)
    return d3crypt3d

def m41n():
    if len(sys.argv) != 2:
        print("Usage: python leetbreaker.py <k^(3)^y>")
        sys.exit(1)

    h3x_1nput = "79737e78647d7b277e7b2a292f2e7b282a2a2929287a7d28287e2d2a287b2a7c2d282e2e2662"
    k3y = int(sys.argv[1])

    d3crypt3d_m3ss4ge = d3crypt_m3ss4ge(h3x_1nput, k3y)
    print("D3crypt3d m3ss4ge:", d3crypt3d_m3ss4ge.decode())

if __name__ == "__main__":
    m41n()
```
ซึ่งพี่ chatgpt ของเราก็ reverse ออกมาหน้าตาแบบนี้ โดยเจ้าตัวจะทำการ brute force key ไปเรื่อยๆ จนกว่าจะ match ค่า hex mี่กำหนดไว้

```python
def d3crypt_m3ss4ge(h3x_1nput, k3y):
    k3y = k3y % 256
    byt3_d4t4 = bytes.fromhex(h3x_1nput)
    d3crypt3d = bytes(b ^ k3y for b in byt3_d4t4)
    return d3crypt3d

def brute_force_key():
    h3x_1nput = "79737e78647d7b277e7b2a292f2e7b282a2a2929287a7d28287e2d2a287b2a7c2d282e2e2662"
    
    for k3y in range(256):  # Try all keys from 0 to 255
        try:
            d3crypt3d_m3ss4ge = d3crypt_m3ss4ge(h3x_1nput, k3y)
            decoded_message = d3crypt3d_m3ss4ge.decode()
            if all(32 <= ord(c) <= 126 for c in decoded_message):  # Check for printable ASCII
                print(f"Key: {k3y} -> Message: {decoded_message}")
        except UnicodeDecodeError:
            # Ignore invalid decoding attempts
            pass

if __name__ == "__main__":
    brute_force_key()
```

![image](https://github.com/user-attachments/assets/fe9fb90b-2346-4fdd-9381-a24b6545905e)

จบ match ที่ key 31 ก็เป็นอันได้ flag ออกมา

