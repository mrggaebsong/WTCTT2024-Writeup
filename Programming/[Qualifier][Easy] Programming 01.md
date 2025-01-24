ได้ python code มา 2 ตัว คือ malxor.py กับ xor.py

```python
# malxor.py
def x47t9v2q3w():
    c6f9x2v8b1 = "050f0204185352565501520756015a065202575352575b00025655535a53025006060054071e"
    q7m3j4u8z1 = 99
    s9x2v3w7y0 = 0

    print("con9r@+u1@+ion5, f1@9 i5: ", end="")
    for a3v6h9m2b4 in range(s9x2v3w7y0):
        p1s8x0v7t9 = int(c6f9x2v8b1[2 * a3v6h9m2b4:2 * a3v6h9m2b4 + 2], 16)
        print(chr(p1s8x0v7t9 ^ q7m3j4u8z1), end="")
    print()

if __name__ == "__main__":
    x47t9v2q3w()
```

```python
# xor.py
def x47t9v2q3w():
        c6f9x2v8b1 = "050f0204185352565501520756015a065202575352575b00025655535a53025006060054071e"
        q7m3j4u8z1 = 99
        s9x2v3w7y0 = len(c6f9x2v8b1) // 2

    print("con9r@+u1@+ion5, f1@9 i5: ", end="")
    for a3v6h9m2b4 in range(s9x2v3w7y0):
        p1s8x0v7t9 = int(c6f9x2v8b1[2 * a3v6h9m2b4:2 * a3v6h9m2b4 + 2], 16)
        print(chr(p1s8x0v7t9 ^ q7m3j4u8z1), end="")
    print()

if __name__ == "__main__":
    x47t9v2q3w()
```

รู้แค่ว่าเล่นกับ XOR แต่เนื่องจากข้าพเจ้าความรู้ด้านโปรแกรมมิ่งแทบเป็น 0 เลยปล่อยให้ chatgpt ทำหน้าที่ของมัน
คำอธิบายจาก chatgpt
> Key Observation
> 
> - Both scripts decode a hexadecimal string `c6f9x2v8b1` by XORing each byte with the integer `q7m3j4u8z1` (99).
> - The difference between the two scripts lies in the value of `s9x2v3w7y0`:
>     - In `malxor.py`, `s9x2v3w7y0 = 0`, so the loop does not execute. This means `malxor.py` does not output the flag.
>     - In `xor.py`, `s9x2v3w7y0 = len(c6f9x2v8b1) // 2`, which calculates the number of bytes in the hexadecimal string. Therefore, `xor.py` runs the loop correctly to decode the flag.
> 
> Solution Steps
> 
> - Parse the hexadecimal string `c6f9x2v8b1`.
> - Convert each byte to an integer.
> - XOR the integer with `q7m3j4u8z1` (99).
> - Convert the result back to characters.
> - Concatenate the characters to form the flag.

reverse แก้โค้ดออกมาได้แบบนี้

```python
def decode_flag():
    c6f9x2v8b1 = "050f0204185352565501520756015a065202575352575b00025655535a53025006060054071e"
    q7m3j4u8z1 = 99  # XOR key
    s9x2v3w7y0 = len(c6f9x2v8b1) // 2  # Number of bytes in the hex string

    decoded_flag = ""
    for a3v6h9m2b4 in range(s9x2v3w7y0):
        # Extract two characters, convert to int, XOR with the key, and decode to ASCII
        p1s8x0v7t9 = int(c6f9x2v8b1[2 * a3v6h9m2b4:2 * a3v6h9m2b4 + 2], 16)
        decoded_flag += chr(p1s8x0v7t9 ^ q7m3j4u8z1)

    print("Decoded Flag: ", decoded_flag)

# Execute the function
decode_flag()
```

![image](https://github.com/user-attachments/assets/15afbd46-20da-4bd1-ac07-c8e562dd66c9)
