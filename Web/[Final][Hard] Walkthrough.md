> เดินให้ทั่วแล้วจะเห็น Flag
> 
> https://walkthrough.sspk.xyz
> 
>หมายเหตุ รูปแบบของ Flag ที่เป็นคำตอบของข้อนี้คือ flag{…}
> 

เปิดหน้าเว็บขึ้นมา ไม่เจออะไรนอกจาก link เยอะแยะเต็มไปหมด

![image](https://github.com/user-attachments/assets/dfc73f4c-9630-4f62-aeac-e56d7ab88615)

จิ้มไปอันนึง ก็จะเจอหน้าย่อยที่มี link ซ้อนอยู่ข้างใน ซึ่งอนุมานได้ว่าแต่ละ link ทุกอันจะมี link ซ้อนย่อยเข้าไปข้างในหลายๆชั้นแน่ๆ

![image](https://github.com/user-attachments/assets/4c4bcea3-c1c5-4162-a9eb-d6084576247b)

กดดู page source จะพบว่าในทุกๆหน้าจะมี comment เป็น `flag{xxxxxxxxxx}` ทั้งหมด แปลว่าเราต้องทำการ crawl webpage เพื่อค้นหา flag ที่แท้จริงที่ซ่อนอยู่ในซักหน้ากลางดง flag ปลอมมากมายมหาศาลแบบนี้ ซึ่งถ้าไป manual เองคือขี้แตกแน่นอน

![image](https://github.com/user-attachments/assets/9f4a63fa-5d8f-45f9-af4e-5f92d8cbf7d3)

ว่าแล้วก็เขียนโค้ด crawler ไปดึง content ออกมา โดยกำหนดเงื่อนไขให้ loop ไปเรื่อยๆ จนกว่าจะเจอ flag ที่ถูกต้อง (เราตั้ง condition เป็น flag จริงจะมีตัวเลขอยู่ จะได้เป็นการกรองตัว xxxx พรืดๆออกไปให้ไม่รก output)

```python
import requests
from bs4 import BeautifulSoup
import re
from urllib.parse import urljoin, urlparse

# Base URL of the challenge site
BASE_URL = "https://walkthrough.sspk.xyz/"

# Set to keep track of visited URLs
visited = set()
flag_found = False  # Global flag to stop when condition is met

def crawl(url):
    """Recursively crawls the website to find the flag that contains a number."""
    global flag_found

    if url in visited or flag_found:
        return  # Stop if already visited or flag is found

    print(f"[*] Crawling: {url}")
    visited.add(url)

    try:
        # Fetch the page content
        response = requests.get(url)
        response.raise_for_status()  # Raise an error for failed requests

        # Check for the flag pattern
        flag_matches = re.findall(r'flag\{.*?\}', response.text)
        for flag in flag_matches:
            if any(char.isdigit() for char in flag):  # Check if flag contains a number
                print(f"[!] Flag found: {flag}")
                flag_found = True
                return

        # Parse the HTML to find links
        soup = BeautifulSoup(response.text, 'html.parser')
        links = soup.find_all('a', href=True)

        for link in links:
            next_url = urljoin(url, link['href'])  # Construct absolute URL

            # Ensure we stay within the same domain
            if urlparse(next_url).netloc == urlparse(BASE_URL).netloc:
                crawl(next_url)  # Recursive call

    except requests.exceptions.RequestException as e:
        print(f"[X] Error fetching {url}: {e}")

# Start crawling from the homepage
crawl(BASE_URL)
```

เสร็จแล้วก็รันสคริป ปล่อยมันไหลไป นั่งรอเอา flag แต่ต้องใช้เวลานิดนึงเพราะเส้นทางมันเยอะมาก น่าจะไม่ต่ำกว่าหลักพัน paths
หลังจากนั่งรอจนเกือบหลับ ก็เจอ flag จริงๆอยู่ที่ `https://walkthrough.sspk.xyz/FOOLEROH.html`

![image](https://github.com/user-attachments/assets/5f10869d-d754-489b-9359-fc106baafac7)
