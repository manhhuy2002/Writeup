 # KCSC_CTF Writeup - Trần Mạnh Huy
 
 ## Table of content Prog_Misc challenge:
* [Đề 1: Xỏ](#Đề-1-xỏ)
* [Đề 2: Gen Max Number](#Đề-2-gen-max-number)
* [Đề 3: Splitting](đề-3-splitting)

 ## Đề 1: Xỏ
 
[1.jpg](https://bcm.kcslab.asia/files/5b15629bd8aa6e8596a23b25fdecd347/1.jpg?token=eyJ1c2VyX2lkIjo1OCwidGVhbV9pZCI6bnVsbCwiZmlsZV9pZCI6NDF9.Y8ZQ1A.o7GnwPOZqcXHwTxEdezXwAyn__I)
 
[2.jpg](https://bcm.kcslab.asia/files/0ad891166bb4da976666f3ae3d606f1f/2.jpg?token=eyJ1c2VyX2lkIjo1OCwidGVhbV9pZCI6bnVsbCwiZmlsZV9pZCI6NDJ9.Y8ZQ1A.XPA6vAmR3w0A0CAwe2iXao-71lQ)
 
 ```
 Cứ byte nào khác nhau thì xor là ra flag
 ```
 
 Bài cho mình 2 ảnh với nội dung cứ byte nào khác nhau thì xor là ra flag, đến đây em nghĩ ngay tới việc xor byte giữa 2 ảnh.
 Vì vậy em viết script đọc byte của 2 ảnh và tìm các byte khác nhau rồi xor lại với nhau:
 ```
 f1 = open('1.jpg', 'rb')
readByteF1 = f1.read()
f2 = open('2.jpg', 'rb')
readByteF2 = f2.read()
for i in range(max(len(readByteF1),len(readByteF2))):
    if readByteF1[i] != readByteF2[i]:
        xor = readByteF1[i] ^ readByteF2[i]
        flag+=chr(xor)
print(flag)
 ```
Flag được in ra là: KCSC{Da_bao_roi_cu_x%r_tung_byte_la_ra_ma_li}

 >Flag: KCSC{Da_bao_roi_cu_x%r_tung_byte_la_ra_ma_li}
 
 
## Đề 2: Gen Max Number
  
```
  Từ số đã cho sắp xếp các chữ số thành một số lớn nhất ví dụ: 4567123098 -> 9876543210

  nc 146.190.115.228 10464
```
nc 146.190.115.228 10464 trên kali linux lên em được 1 số: 996207946162837670, dạng này yêu cầu phải giải được trong 1 thời gian quy định và phải giải quyết nhiều payload liên tục nên em viết script để tự xử lí và tự nhập kết quả vào. Ở đây em dùng script python để sắp xếp lại các thành 1 số lớn nhất với ý tưởng chia các phần tử thành mảng rồi sắp xếp theo chiều giảm dần rồi nối lại với nhau bằng join. Và dùng socket để kết nối xử lí bài này.

Code python: 
```
def max_number(number):
    arr = []
    a = int(number)
    while a>0:
        arr.append(str(a%10))
        a = a//10
    b= sorted(arr)
    b.reverse()
    return "".join(b)
    
 ```
 
 socket:
 ```
 import socket 
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(('146.190.115.228',  10464))
buffer = ''
buffer += sock.recv(1024).decode('utf-8')
print(buffer)

```
Đến đây em chạy thử socket xem kết nối được chưa: 

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/regex0.jpg)

Bây giờ chỉ cần cắt đoạn số ra rồi dùng code trên xử lí, ở đây em dùng regex:

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/flag0.jpg)

Sau đó em viết tiếp vào script in thử xem có ra số mình cần regex chưa thì đã ổn:

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/last.jpg)

Vậy nên tiếp theo em đưa đoạn script sắp xếp chuỗi ở trên vào để xử lí:
 
```
import socket 
import re
rNumber = r"Number: ([0-9]+)\n"
def max_number(number):
    arr = []
    a = int(number)
    while a>0:
        arr.append(str(a%10))
        a = a//10
    b= sorted(arr)
    b.reverse()
    return ''.join(b)     
 
        
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(('146.190.115.228', 10464))
try:
    while True:
        buffer = ''
        buffer += sock.recv(1024).decode()
        print(buffer)
        match = re.search(rNumber, buffer)
        if match:
            a = int(match.group(1).strip())
        print(a)
        flag = max_number(a)
        sock.send(str(flag+"\n").encode())
        print(flag)        
except:
    print(buffer)
```

Vậy là flag được trả về: 

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/flag.jpg)



>Flag: KCSC{You will maximize yourself when participating in KCSC, G00d_j0b}

## Đề 3: Splitting


### Sử dụng thư viện wordninja split chuỗi có nghĩa  để cắt ra các từ có nghĩa từ 1 chuỗi hỗn tạp
```
import socket
import wordninja
import re

regex = (r"Split it: ([A-Za-z'0-9]+)\n"
  r"Ans")
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(("146.190.115.228",10463))
try :
    while True :
        buffer = ""
        buffer+=sock.recv(1024).decode()
        print(buffer)
        matches = re.search(regex,buffer)
        n = matches.group(1)
        c= wordninja.split(n)
        res = " ".join(c)
        sock.send((res+"\n").encode())
        print(res)
except:
    print(buffer)
    
```

