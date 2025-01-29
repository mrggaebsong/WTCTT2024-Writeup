เปิดไฟล์ word ขึ้นมา เจออิโมจิเป็นพรืดเป็นแถวเลย

![image](https://github.com/user-attachments/assets/2b524555-41e8-456d-bcef-cdeef219c2f6)

ตอนแรกหาวิธีแกะกันแทบตายก็ไม่ออกซะที จนกระทั่งมาเจอคลิปนี้ที่เรียกได้ว่า Life Saver >> [Decrypt emoji cipher by python](https://www.youtube.com/watch?v=2s8d9vCVHAc)

จากในคลิปนี้ โค้ดจะทำการ decode emoji เป็นค่า unicode code points ซึ่งถ้าถอดออกมา

```python
# Define the emoji cipher text
emoji_cipher = "😒😘😍😓😧😕😋😘😛😢😑😋😑😙😛😖😕😋😚😛😋😙😍😠😠😑😞😋😣😔😍😠😋😕😋😟😠😕😘😘😋😘😛😢😑😋😑😙😛😖😕😋🗠🗝🗣🗝🗡🗞🗝🗡🗜🗥😩"

# Convert emojis to Unicode code points
unicode_points = [ord(char) for char in emoji_cipher]
unicode_points
```

![image](https://github.com/user-attachments/assets/813d0b9d-87ad-439e-acb7-f9f9c422860a)

ทีนี้เราจะสังเกตเห็นว่า unicode ทุกตัวที่ถอดได้เป็นตัวเลข ทุกตัวมีค่ามากกว่า 128500 เพื่อให้ดูง่าย ก็จัดการลบด้วย 128500 ออกจากทุกค่าซะ

```python
# Subtract 128500 from each Unicode code point
adjusted_unicode_points = [code - 128500 for code in unicode_points]
adjusted_unicode_points
```

![image](https://github.com/user-attachments/assets/86ea47bd-3c62-4358-9e52-e09d076b2669)

ออกมาเป็นตัวเลขที่ดูอ่านง่ายกว่าเดิมละ ซึ่งดูแล้วก็ออกมาเหมือนจะเป็น ASCII code เพื่อป้องกันการเหลี่ยม เรารู้ format ของ flag แล้วคือ `flag{…}` ไหนลองเช็คเลข ASCII ของตัว flag ซิ

```
flag = 102 108 97 103
```

จะเห็นส่วนต่างของ ASCII ที่เราได้กับ flag ที่แท้จริงห่างกันอยู่ 72 ทุกตัว ทีนี้ก็ทำการบวกเพิ่มเข้าไปอีก 72 ที่รหัส ASCII ให้เป็นค่าจริง

```python
# Add 72 to each adjusted Unicode code point
ascii_adjusted_points = [code + 72 for code in adjusted_unicode_points]

# Decode into characters (filter valid ASCII values: 0-127)
decoded_message = ''.join(chr(code) for code in ascii_adjusted_points if 0 <= code <= 127)

ascii_adjusted_points, decoded_message
```

เราจะได้โค้ดฉบับเต็มดังนี้

```python
# Define the emoji cipher text
emoji_cipher = "😒😘😍😓😧😕😋😘😛😢😑😋😑😙😛😖😕😋😚😛😋😙😍😠😠😑😞😋😣😔😍😠😋😕😋😟😠😕😘😘😋😘😛😢😑😋😑😙😛😖😕😋🗠🗝🗣🗝🗡🗞🗝🗡🗜🗥😩"

# Convert emojis to Unicode code points
unicode_points = [ord(char) for char in emoji_cipher]

# Subtract 128500 from each Unicode code point
adjusted_unicode_points = [code - 128500 for code in unicode_points]

# Add 72 to each adjusted Unicode code point
ascii_adjusted_points = [code + 72 for code in adjusted_unicode_points]

# Decode into characters (filter valid ASCII values: 0-127)
decoded_message = ''.join(chr(code) for code in ascii_adjusted_points if 0 <= code <= 127)

# Display results
{
    "Unicode Points": unicode_points,
    "Adjusted Points (subtract 128500)": adjusted_unicode_points,
    "ASCII Adjusted Points (add 72)": ascii_adjusted_points,
    "Decoded Message": decoded_message,
}
```

เรายังมี version rewrite ใหม่ให้โค้ดสวยและกระชับกว่าเดิม

```
# Define the emoji cipher text
emoji_cipher = "😒😘😍😓😧😕😋😘😛😢😑😋😑😙😛😖😕😋😚😛😋😙😍😠😠😑😞😋😣😔😍😠😋😕😋😟😠😕😘😘😋😘😛😢😑😋😑😙😛😖😕😋🗠🗝🗣🗝🗡🗞🗝🗡🗜🗥😩"

# Decode the message by applying transformations
decoded_message = ''.join(
    chr((ord(char) - 128500) + 72) for char in emoji_cipher if 0 <= (ord(char) - 128500) + 72 <= 127
)

decoded_message
```

![image](https://github.com/user-attachments/assets/b50fd04a-7504-4b5f-8e6f-6a0dee0d7f37)
