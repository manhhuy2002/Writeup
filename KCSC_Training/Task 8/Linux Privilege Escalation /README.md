
## Linux Privilege Escalation | Tryhackme


> https://tryhackme.com/room/linprivesc

Đầu tiên là kết nối với máy chủ thông qua ssh, sau khi kết nối thành công ta sẽ viết lại 1 số lệnh quen thuộc cũng như định nghĩa của nó.

**hostname**: tên của máy chủ mục tiêu trong mạng và giúp các thiết bị khác có thể truy cập vào nó qua mạng, ở đây hostname là: wade7363

**uname -a**: Linux wade7363 3.13.0-24-generic #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
lệnh này cho ta biết chi tiết thông tin về hệ điều hành (linux), tên hostname (wade7367), 3.13.0-24-generic (phiên bản kernel đang chạy), #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014 (thông tin về kernel đó được biên dịch trên hệ thống), x86_64 (kiến trúc máy tính - 64 bit), x86_64 (kiến trúc kernel đang chạy trên hệ thống), x86_64 ( kiến trúcc cpu), gnu/linux (hệ điều hành đang chạy ở đây là gnu/linux), với -a ở đây là all. 

**/proc/version**: Linux version 3.13.0-24-generic (buildd@panlong) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014
Lệnh này cung cấp thông tin về phiên bản kernel, tên và ngày phát hành của hệ điều hành Linux đang chạy trên hệ thống.

**/etc/issue**: Ubuntu 14.04 LTS
Chứa thông tin về phiên bản hệ điều hành đang chạy trên máy, tuy vậy thì file này có thể chỉnh sửa và thay đổi được. 

Một lệnh nữa khá thường gặp là **ps**: cung cấp cho ta thông tin về các tiến trình đang chạy trên hệ thống linux.KHi thực thi lệnh ps nó sẽ hiển thị một số thành phần sau: 
PID: định danh duy nhất cho mỗi tiến trình (ID)
TTY: loại terminal được sử dụng bởi người dùng cho tiến trình đó. 
TIME: thời gian CPU được sử dụng bởi tiến trình đó. 
CMD: lệnh hoặc chương trình đang chạy. 
Một vài option hay đi kèm như -A hay aux: 
ps -A: hiển thị tất cả các tiến trình đang chạy. 
ps aux: a hiển thị tất cả các tiến trình cho tất cả các user, u hiển thị user đã khởi chạy tiến trình và x hiển thị các tiến trình kh được gắn vào terminal. 


**env**: hiển thị biến môi trường trong hệ thống, cụ thể ở đây: 

```
AIL=/var/mail/karen
USER=karen
SSH_CLIENT=10.8.84.31 44684 22
HOME=/home/karen
SSH_TTY=/dev/pts/4
QT_QPA_PLATFORMTHEME=appmenu-qt5
LOGNAME=karen
TERM=xterm-256color
XDG_SESSION_ID=1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
XDG_RUNTIME_DIR=/run/user/1001
LANG=en_US.UTF-8
SHELL=/bin/sh
PWD=/
SSH_CONNECTION=10.8.84.31 44684 10.10.238.166 22

```

Biến path ở đây có thể chứa trình biên dịch hoặc ngôn ngữ lập trình mà có thể chạy và thực thi code trên hệ thống mục tiêu hoặc dùng để leo thang đặc quyền, ta sẽ tìm hiểu kĩ hơn ở sau. 

**sudo -l**: nếu máy chủ mục tiêu có configured cho phép user thực hiện 1 số quyền root nhất định, thì ta có thể dùng lệnh này để xem, còn không thì dùng lệnh này sẽ phải nhập pass. Và ta cũng có thể khai thác được leo thang đặc quyền từ nó trong một số trường hợp. 

**id**: lệnh id thì cho ta biết một cái nhìn tổng quan về mức độ đặc quyền của người dùng và tư cách thành viên nhóm.

**/etc/passwd**: một cách dễ dàng để biết về những users trên hệ thống.
Muốn lấy ngắn gọn danh sách các người dùng trong hệ thống ta chỉ cần dùng thêm lệnh cut: **cat /etc/passwd | cut -d ":" -f 1** để lấy, trong đó thì để dễ nhớ thì -d ở đây là delimiter dùng để chỉ định dấu phân tách các trường với dấu : , và -f 1 dùng để chỉ định trường dữ liệu được lấy ra từ các trường đã được phân tách và ở đây lấy vị trí số 1. Hoặc 1 lệnh quen thuộc cũng thường xuyên được dùng là cat /etc/passwd | grep -i "flag.txt" trong đó thì grep dùng để tìm kiếm và -i thì là ignore chữ hoa chữ thường. 


**history**: lệnh history có thể được sử dụng để gợi ý cho ta về các lệnh trước đó đã được sử dụng trên hệ thống mục tiêu và có thể cung cấp cho chúng ta một số ý tưởng về hệ thống đó và đôi khi có thể lưu trữ thông tin như mật khẩu hoặc tên người dùng. 

<hr> 

**find**: lệnh find này cũng khá thường xuyên được sử dụng để tìm kiếm flag hoặc file gì đó mà ta quên hoặc chưa biết vị trí, và đôi khi có thể được dùng để leo thang đặc quyền, mình sẽ demo sau. 
Cụ thể thì ta có ví dụ: 
find . -name flag.txt: tìm file có tên là flag.txt ở thư mục hiện tại trở đi. 
find / -type d -name config: tìm directory có tên là config từ / trở đi. 
find / -type f -perm 0777: tìm các file có quyền là 0777
find / -perm a=x: tìm các file có quyền thực thi
find /home -user frank: tìm các file của người dùng frank dưới /home. 
...

Còn 1 vài cái nữa hay dùng nữa là: 

```
find / -writable -type d 2>/dev/null
find / -perm -222 -type d 2>/dev/null
find / -perm -o w -type d 2>/dev/null

perm trong lệnh find là tùy chọn dùng để tìm kiếm các tập tin hoặc thư mục có quyền truy cập nhất định. Nó cho phép người dùng chỉ định các quyền truy cập cụ thể mà tập tin hoặc thư mục cần phải có để được tìm kiếm.

```

lệnh dùng để tìm các thư mục có quyền ghi, trong đó thì 2>/dev/null là một đoạn ghi đè (redirection) đầu ra của lệnh, có nghĩa là đầu ra thông báo lỗi của lệnh sẽ không được hiển thị trên màn hình mà sẽ được gửi đến thiết bị /dev/null (thiết bị này không lưu trữ dữ liệu và không trả về gì khi được ghi vào). Ký tự 2 ở đây tượng trưng cho đầu ra lỗi (stderr), trong khi > là toán tử redirection để chuyển đầu ra sang một tệp hoặc thiết bị khác. Bằng cách này, nếu có bất kỳ lỗi nào xảy ra khi chạy lệnh find, chúng sẽ không được hiển thị trên màn hình và sẽ bị loại bỏ hoàn toàn.

<hr> 

**SUID**: viết tắt của set user id. Khi một tệp có quyền SUID được thực thi, nó được thực thi với đặc quyền của chủ sở hữu của nó thay vì người dùng thực thi nó. Ví dụ, nếu một tệp có quyền SUID được sở hữu bởi người dùng "root" và được thực thi bởi một người dùng khác, nó sẽ chạy với đặc quyền "root" thay vì đặc quyền của người dùng thực thi nó.
Ta cũng có thể dùng lệnh find để tìm kiếm các file này: **find / -perm -u=s -type f 2>/dev/null**
Trong đó thì -u=s ở đây sẽ tìm kiếm quyền SUID được thiết lập cho người sở hữu của tập tin, và ta có thể dùng nó để leo thang đặc quyền.

> Một số link tools hay: LinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS
LinEnum: https://github.com/rebootuser/LinEnum
LES (Linux Exploit Suggester): https://github.com/mzet-/linux-exploit-suggester
Linux Smart Enumeration: https://github.com/diego-treitos/linux-smart-enumeration
Linux Priv Checker: https://github.com/linted/linuxprivchecke


<hr> 

Đầu tiên ta sẽ đi vào phần khai thác Privilege Escalation: Kernel Exploits
Mục đích của bài lab này là leo thang đặc quyền và đọc được file flag1.txt

![image](https://user-images.githubusercontent.com/104350480/236391599-11e835ca-b5d4-4340-afb1-df6b44b4060f.png)

Dùng lệnh find như trên mà tìm kiếm được vị trí, giờ ta cần leo lên root để đọc file này, như gợi ý đề bài thông qua kernel exploits, và ở phần trước nó cũng cho mình một gợi ý khi tìm thông tin về cve của kernel, với kernel là 3.13.0-24-generic thì search mạng ta được cve là : CVE-2015-1328.

Vì vậy ta chỉ cần lên mạng search thêm exploit là xong, vấn đề là tryhackme chỉ cho phép down dữ liệu thông qua mạng openvpn họ cấp nên ta sẽ down về máy mình rồi dựng server lên rồi wget qua nó vậy. 

> link tải về: https://www.exploit-db.com/exploits/37292

Mà tải về chỉ được 1 file là 37292 với loại tập tin với mã nguồn c, ta mv sang file .c với thành 37292.c . Sau đó dùng lệnh gcc để cấp quyền, sau đó ta sử dụng lệnh **gcc 37292.c -o flag** biên dịch mã nguồn C trong tập tin 37292.c thành một chương trình có thể thực thi được và lưu vào tập tin có tên là flag.

Sau đó ta chỉ cần thực thi lệnh để chuyển sang root, code này mình chắc để học thêm rồi phân tích sau chứ nhìn hơi choáng 🧔

![image](https://user-images.githubusercontent.com/104350480/236413652-f16c4c6b-54ca-45b6-84cd-56bc752399e7.png)


Ta được flag: 

![image](https://user-images.githubusercontent.com/104350480/236411774-45af7a77-3a81-4461-bc2b-776feac9a3db.png)


<hr> 


## Task 6: Privilege Escalation: Sudo

Mình sẽ tóm gọn lại nội dung của phần task này trên tryhackme

Đầu tiên giới thiệu về sudo, thì ta có thể hiểu đơn giản là lệnh sudo cho phép ta chạy với đặc quyền root, thông thường thì phải có mật khẩu hoặc được cấp để sử dụng nó. Tuy vậy thì trong thực tế, các admin có thể cần cấp cho người dùng thông thường một số độ linh hoạt về quyền hạn. Ví dụ, một nhân viên phân tích SOC mới có thể cần sử dụng Nmap thường xuyên nhưng không được cấp quyền truy cập root đầy đủ. Trong trường hợp này, quản trị viên hệ thống có thể cho phép người dùng này chỉ chạy Nmap với quyền root trong khi giữ nguyên cấp độ đặc quyền thông thường trong phần còn lại của hệ thống. Thông thường có thể kiểm tra nó bằng lệnh sudo -l để biết user đó có được cấp gì quyền root hay không. 

> Một trang web khá hay về leo thang đặc quyền: https://gtfobins.github.io/

![image](https://user-images.githubusercontent.com/104350480/236420250-c1316746-e308-4802-b64d-5b6f2636f187.png)

Như ở đây là karen đang có 3 quyền thực thi root trên find, less và nano, ta cũng có thể làm tương tự điều này bằng cách thêm 1 user và thêm quyền vào trong file /etc/sudoers và tự khai thác để hiểu hơn về cách thức, nhưng ta sẽ khai thác ở đây luôn. 

Đầu tiên đối với nano ta có thể leo thang bằng cách: mở nano lên và ctrl R ctrl X rồi nhập reset; sh 1>&0 2>&0 là ta sẽ lên root và thực thi được lệnh trong nano luôn. Sau đấy thì có thể đọc file flag2.txt như bình thường. Trong đó thì khi ta sử dụng ctrl R và ctrl X nó cho phép ta sử dụng tính năng "execute-command" của nano. Khi nhập "reset; sh 1>&0 2>&0" vào đó và nhấn Enter, lệnh "reset" sẽ xóa bảng điều khiển của ta và chuẩn bị màn hình cho một lệnh mới. Sau đó, lệnh "sh" sẽ chạy, đưa bạn vào chế độ shell với quyền root, do đó bạn có thể thực hiện các hành động với quyền root

![image](https://user-images.githubusercontent.com/104350480/236421661-30931255-fb7f-4560-85d8-216a0f946945.png)

Trước mình cũng nhìn mấy lệnh này nhưng cũng kh hiểu nó lắm, nhất là 1>&0 và 2>&0 thì 1>&0 và 2>&0 ở đây là các lệnh redirect trên linux, nó có chức năng đưa ra đầu ra tiêu chuẩn stdout và đầu ra lỗi stderr của một chương trình về cùng địa chỉ với đầu vào tiêu chuẩn (stdin). Trong trường hợp này, 1>&0 và 2>&0 được sử dụng để đưa đầu ra tiêu chuẩn và đầu ra lỗi của shell script đang chạy về địa chỉ tương đương với đầu vào tiêu chuẩn, tức là đưa chúng về terminal hiện tại. Khi đó, shell sẽ đưa đầu vào của người dùng (input) vào terminal, và các lệnh trong shell script sẽ được thực thi với quyền root.

Tương tự ta cũng có thể làm điều đó với lệnh find, với cmd để leo thang: **sudo find . -exec /bin/sh \; -quit**. Ở đây thay vì dùng để tìm kiếm như thông thường thì nó tìm kiếm được file nào cái thì nó sẽ dùng -exec /bin/sh để thực thi 1 shell và leo lên quyền root. \; để kết thúc lệnh này và khi tìm thấy và thực thi thì nó sẽ quit ngay lập tức với -quit. 

Tương tự với less:

```
sudo less /etc/profile
!/bin/sh

Dùng ! để thực thi 1 shell khác với quyền root và ở đây là shell /bin/sh (/bin/sh là một loại shell Unix standard, được sử dụng để thực thi các lệnh trên hệ thống Unix-like. Shell này cung cấp một giao diện dòng lệnh cho người dùng để tương tác với hệ thống).
```
Tương tự ta có thể thực thi nhiều loại khác nữa miễn là được cấp quyền thì hoàn toàn có thể leo thang. 

Trong phần này còn có cái Leverage LD_PRELOAD mình chưa hiểu lắm, nhưng ở sau mình sẽ thực thi demo, kiểu gì cũng phải hiểu =)))

<hr>


Tiếp tục đến với phần SUID, ở phần này sẽ kh còn liên quan đến sudo nữa ta sẽ tiếp tục với SUID đã được nhắc loáng thoáng ở trên: 

![image](https://user-images.githubusercontent.com/104350480/236432185-be612a79-31d5-4bb7-8f75-94b5f93ce418.png)

Nhắc lại, SUID (set user identification) là một thuộc tính (bit) trên các tập tin trên hệ thống Linux/Unix, cho phép tập tin được thực thi với đặc quyền của chủ sở hữu của nó thay vì người dùng thực thi nó. Khi một tập tin được đặt thuộc tính SUID, nó sẽ được thực thi với đặc quyền của chủ sở hữu của tập tin đó thay vì người dùng thực thi nó. Ví dụ, nếu tập tin đó thuộc về người dùng có quyền root, khi được thực thi, tập tin sẽ được thực thi với quyền root thay vì quyền người dùng thực thi nó.

Có thể dùng lệnh **find / -type f -perm -04000 -ls 2>/dev/null** để liệt kê ra các file có cài đặt SUID và SGID trong hệ thống (Bit SUID được thiết lập bằng cách sử dụng số octal 4xxx ). 

![image](https://user-images.githubusercontent.com/104350480/236434183-ef4f1808-5284-4eaa-bb1c-99340c5fbde2.png)

Chẳng hạn nếu ở đây nano thuộc sở hữu của người dùng root, ta có 2 option để có thể leo thang đặc quyền một là đọc file /etc/shadow 2 là thêm 1 user vào /etc/passwd. Nhưng ở đây kh có nano, ta sẽ tìm kiếm 1 lệnh khác. Tuy vậy ở đây ta có base64 đang được set ở chế độ SUID:

![image](https://user-images.githubusercontent.com/104350480/236629441-16a7455d-d17d-48eb-8052-b6da6f939956.png)

Ta sẽ thực thi lệnh và phân tích shell: 

Đầu tiên là lệnh: **LFILE=file_to_read** LFILE ở đây dùng để gán thôi nên dùng cái gì cũng được, file ta sẽ đọc ở đây chẳng hạn là /etc/shadow
Sau đó dùng cmd: **base64 "$LFILE" | base64 --decode** để thực thi mã hóa base64 và giải mã nso rồi hiển thị ra màn hình, ta được:

![image](https://user-images.githubusercontent.com/104350480/236629794-195753aa-8a19-4d51-a0ed-8a651b8e0cb8.png)

Từ đây ta có password của user2 là: 

> $6$m6VmzKTbzCD/.I10$cKOvZZ8/rsYwHd.pE099ZRwM686p/Ep13h7pFMBCG4t7IukRqc/fXlA1gHXh9F2CbwmD4Epi1Wgh.Cl.VV1mb/

Vì ta cần dạng rõ nên ta sẽ dùng 1 tool khá hay dùng là john th ripper để crack khóa này, cái này thì ta ném qua bên terminal của mình để chạy thôi. 

![image](https://user-images.githubusercontent.com/104350480/236630325-4b1ac778-adba-4cb5-80a2-0d56d3787172.png)

Chạy cái ta được password: Password1

Còn giờ muốn đọc flag ta chỉ cần dùng base64 để đọc file flag3.txt là được: 

![image](https://user-images.githubusercontent.com/104350480/236630812-9541a064-f989-474d-bb6d-77df95fe61a4.png)

> THM-3847834

<hr> 

## Privilege Escalation: Capabilities

Ta sẽ tiếp tục với 1 cách thức khác để leo thang đặc quyền,  phương thức ở đây là Capabilities. Capabilities giúp quản lí các đặc quyền một cách chi tiết hơn.
Nó là một quyền đặc biệt của tiến trình, cho phép thực hiện một số thao tác đặc biệt mà không cần đầy đủ quyền root của hệ thống. 

Các capabilities có 2 loại chính phân chia dựa trên phạm vi là: Effective (hiệu lực) và Inheritable (kế thừa)

- Effective Capabilities: Chỉ định các Capabilities mà một tiến trình đang sử dụng khi thực hiện một hành động cụ thể. Nếu một tiến trình không có Effective Capability để thực hiện một hành động, thì hành động đó sẽ bị từ chối.
- Inheritable Capabilities: Cho phép các Capabilities được truyền từ tiến trình cha sang tiến trình con. Với Inheritable Capabilities, một tiến trình con có thể được cấp quyền để thực hiện một số hành động đặc biệt, mà không cần đầy đủ quyền đặc quyền của hệ thống.

Trong bài lab này ta sẽ thấy vim và view được cấp 1 giá trị của capabilities là cap_setuid+ep, được sử dụng để cấp quyền đặc biệt cho một chương trình, ta sẽ phân tích chi tiết thành phần này để biết ta leo thang nó dựa trên việc thay đổi gì: 
- Trước hết thì cap_setuid là một capability cho phép một chương trình thay đổi UID hiện tại của mình (tức là có thể đặt UID mới cho quá trình thực thi). Vì vậy, khi một chương trình được cấp quyền cap_setuid, nó có thể thực thi với đặc quyền của một người dùng khác hoặc thực thi dưới đặc quyền của người dùng root.
- Tiếp theo ep: là một thuộc tính được gọi là "effective". Khi một chương trình được cấp quyền với thuộc tính ep, nó có quyền thực thi các hành động có sẵn trong capability mà không cần bất kỳ giới hạn nào.

> Ta có thể kết luận ở đây: cap_setuid+ep, chương trình được cấp quyền để thay đổi UID của mình và có thể thực thi mọi hành động được cho phép trong capability đó mà không gặp bất kỳ giới hạn nào. Điều này cho phép chương trình thực thi dưới quyền root và thực hiện các hành động khác đòi hỏi đặc quyền của root, như là đọc, ghi hoặc thay đổi các tập tin không thể truy cập được bởi người dùng thông thường.

Oke giờ vào phân tích bài cũng như thực thi cmd thôi: 

Trước hết ở đây ta dùng tool getcap để hiển thị hoặc gán các capabilities được áp dụng trong các tệp và thư mục trong hệ thống tệp của linux, vì nó sẽ sinh ra khá là nhiều lỗi nên ở đây ta sẽ dùng thêm 2>/dev/null để chuyển hết lỗi sang thiết bị null, còn -r khá quen rồi: recursive để duyệt các thư mục con: 
cmd: **getcap / -r 2>/dev/null**

![image](https://user-images.githubusercontent.com/104350480/236633253-8dc5703a-6e8d-4261-9b12-88a54c3b3c22.png)

Ở đây dùng link này **https://gtfobins.github.io/** tìm thử xem có capabilities nào không: thì search ta được có vim và view đều có:
chạy cmd của nó là được, cả 2 cái đều chạy lệnh giống nhau, sửa mỗi py thành py3 vì máy của karen chưa cài thì phải, thế là mình sẽ sang được root: 

> ./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'

> ./view -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'

Lệnh này thì ta có thể phân tích như này để nhớ: lệnh này nó thực thi mã python trên hệ thống và leo thang đặc quyền, :py3 là 1 tiền tố để chỉ rằng :py là một tiền tố để chỉ ra rằng đây là mã Python3 cần thực thi. import os; là lệnh để nhập thư viện os của Python. os.setuid(0); là lệnh để đặt UID hiện tại của tiến trình thành 0, tức là đặt UID thành root. Còn os.execl() để thực thi 1 shell (chương trình /bin/sh) với 2 đối số là "sh" và "-c", -c ở đây sẽ là một chuỗi lệnh được thực thi tiếp theo là ***reset; exec sh*** được sử dụng để xóa bất kỳ nội dung nào hiển thị trên màn hình console, sau đó tiếp tục thực thi shell với quyền đặc quyền, nó gần giống vụ nano được cấp root ấy. Hiểu đơn giản hơn thì sẽ là:  /bin/bash là đường dẫn tới shell được sử dụng để thực thi các lệnh trong hàm, shell ở đây có các command là "reset; exec sh", và thế là ta leo quyền. 

Sau khi leo thang thì ta sẽ hoàn thành bài lab với vai trò là root thôi. 


<hr> 


