> คุณเคยเล่น Leet Code ไหม โจทย์ข้อนี้ได้แนวคิดมาจากโจทย์ Two Sum ผมอยากรู้น่ะว่าถ้าเราเพิ่มจากสองเป็นสาม มันจะยากขึ้นไหม
> เพื่อให้ได้ flag คุณต้องหารหัสจาก array นี้
> รหัสแรกคือผลคูณของ array index ทั้งสามที่ทำให้ได้ผลลัพธ์เป็น target (any digit) ex.123456
> รหัสที่สองคือผลบวกของ array index ทั้งสามที่ทำให้ได้ผลลัพธ์เป็น target (3 digit) ex. 079

หลังจากเราแตกไฟล์ zip เราจะได้ไฟล์ APK พร้อมกับ text file ที่อธิบายตัว III sum (ของเราโหลดตัวเก่ามาแล้ว submit ได้ก่อนเขาจะ upload ตัวอย่างใหม่มา แฮ่)

```
[112, 893, 745, 320, 134, 878, 631, 101, 905, 234,
999, 876, 543, 467, 872, 953, 421, 308, 758, 102,
394, 273, 216, 394, 789, 597, 305, 629, 459, 681,
800, 938, 145, 392, 502, 719, 387, 563, 644, 732,
456, 779, 849, 679, 574, 190, 435, 387, 274, 110]
target = 2424

Example 1:
Input: nums = [2,7,4,1,8], target = 13
Output: [0, 1, 3]
Explanation: Because nums[0] + nums[1] + nums[3] == 13, we return [0, 1, 3].

Example 2:
Input: nums = [1,3,5,7,9], target = 15
Output: [0, 2, 3]

Example 3:
Input: nums = [4,6,10,2,8], target = 18
Output: [0, 2, 4]
```

ในไฟล์นี้จะให้ array ตัวเลขมา 50 ตัว โดยค่าเป้าหมายนี้กำหนดเงื่อนไขในโจทย์ว่า ต้องหา **index 3 ตัวที่ไม่ซ้ำกัน** จาก array ที่เมื่อรวมค่าของ index ทั้ง 3 จะได้เท่ากับ 2424 จากสูตร `nums[0] + nums[1] + nums[3] = 2424`
แต่ไม่รู้ว่าเอาค่าไปทำอะไร ลองไปดูโค้ดใน APK ก่อน เริ่มจาก `MainActivity`

```java
/* loaded from: classes4.dex */
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "IIISum";

    @Override // androidx.fragment.app.FragmentActivity, androidx.activity.ComponentActivity, androidx.core.app.ComponentActivity, android.app.Activity
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final EditText userInput = (EditText) findViewById(R.id.user_input);
        Button submitButton = (Button) findViewById(R.id.submit_button);
        submitButton.setOnClickListener(new View.OnClickListener() { // from class: com.example.iiisum.MainActivity$$ExternalSyntheticLambda0
            @Override // android.view.View.OnClickListener
            public final void onClick(View view) {
                MainActivity.lambda$onCreate$0(userInput, view);
            }
        });
    }

    static /* synthetic */ void lambda$onCreate$0(EditText userInput, View v) {
        String input = userInput.getText().toString();
        checkInput(input);
    }

    public static void checkInput(String input) {
        String correctKey = generateComplexKey();
        if (input.equals(correctKey)) {
            Log.d(TAG, "Brilliant! Follow this url and the password is sum of that 3 indexes (3 digit) ex. 079: " + iiisum());
        } else {
            Log.d(TAG, "Try again! Incorrect input.");
        }
    }

    private static String generateComplexKey() {
        int[] numbers = {112, 893, 745, 320, 134, 878, 631, TypedValues.TYPE_TARGET, TypedValues.Custom.TYPE_DIMENSION, 234, 999, 876, 543, 467, 872, 953, TypedValues.CycleType.TYPE_WAVE_SHAPE, 308, 758, LocationRequestCompat.QUALITY_BALANCED_POWER_ACCURACY, 394, 273, 216, 394, 789, 597, 305, 629, 459, 681, 800, 938, 145, 392, TypedValues.PositionType.TYPE_DRAWPATH, 719, 387, 563, 644, 732, 456, 779, 849, 679, 574, 190, 435, 387, 274, 110};
        int sum = 0;
        for (int num : numbers) {
            sum += num;
        }
        int finalResult = (sum / 3) - 448;
        return String.valueOf(finalResult);
    }

    private static String iiisum() {
        byte[] decodedBytes = Base64.getDecoder().decode("aHR0cHM6Ly9wYXN0ZWJpbi5jb20vaXdFQzRzZ2s=");
        return new String(decodedBytes);
    }
}
```

โค้ดจะมีการเช็ค input key จาก `generateComplexKey()` ที่คำนวณค่า III sum ตามตัวอย่างที่ได้มา โดยหลังจากหาค่า index ได้แล้ว จะนำค่ามาใช้คำนวณเพื่อสร้าง flag ที่แบ่งเป็น 2 ส่วน
- ส่วนแรกจะเอา index i, j, k มาคูณกัน
- ส่วนที่ 2 จะเอา รndex i, j, k มาบวกกัน 3 หลัก
กลับมาที่ตัวเช็ค key ถ้า key ในส่วนแรกถูกต้อง ให้ไปที่ URL (อะไรวะ?) แล้วใส่ password เป็นรหัส 3 หลักจาก key ส่วนที่ 2 ก็จะได้ flag ออกมา แต่ก็ยังนึกไม่ออกอยู่ดีว่าจะเอา input ไปเติมตรงไหน
เลื่อนลงไปข้างล่างจะเจอ `iiisum()` ที่มี Base64 strings อยู่ ลองเอาไปแกะดู

![image](https://github.com/user-attachments/assets/df695664-b602-49b0-b764-89d0849f22fe)

จะได้เป็น Pastebin URL หรือว่านี่จะเป็น URL ที่เราต้องไปใส่ key ชุด 2 เพื่อเอา flag ว่าแล้วก็ไปกันเล้ย

![image](https://github.com/user-attachments/assets/7ab14e7b-f968-4a75-884a-2d5709eefa48)

เข้าไปจะเจอล็อคให้เราใส่รหัสก่อน ซึ่งเราต้องเอา key ที่เป็นรหัส 3 ตัวมาใส่ ว่าแล้วก็เอาโค้ดใน APK ไป reverse หา key กันเล้ย

```python
def find_three_indices(nums, target):
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            for k in range(j + 1, n):
                if nums[i] + nums[j] + nums[k] == target:
                    return [i, j, k]
    return []

nums = [112, 893, 745, 320, 134, 878, 631, 101, 905, 234,
        999, 876, 543, 467, 872, 953, 421, 308, 758, 102,
        394, 273, 216, 394, 789, 597, 305, 629, 459, 681,
        800, 938, 145, 392, 502, 719, 387, 563, 644, 732,
        456, 779, 849, 679, 574, 190, 435, 387, 274, 110]

# Given target
target = 2424

# Find indices
indices = find_three_indices(nums, target)
if indices:
    index_product = indices[0] * indices[1] * indices[2]
    index_sum = sum(indices)

    # Format results for the flag
    flag_product = str(index_product)  # any digit
    flag_sum = f"{index_sum:03}"  # 3-digit format

    print("Flag part 1 (product):", flag_product)
    print("Flag part 2 (sum):", flag_sum)
else:
    print("No valid indices found.")
```

![image](https://github.com/user-attachments/assets/2565c6b2-f812-44a9-a0b4-ba49ce313c9f)

จะได้ key 2: sum เป็นรหัส 073 ลองเอาไปใส่ใน pastebin

![image](https://github.com/user-attachments/assets/d1d1dcbd-4fa8-4235-8b29-3b235c5c83d6)
