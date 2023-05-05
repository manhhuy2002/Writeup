# Ta sẽ đi tìm hiểu cơ bản nhất về leo thang đặc quyền trong linux: 


## what is shell? 

Trước khi đi vào chi tiết về gửi và nhận shell, điều quan trọng là phải hiểu rõ shell là gì? Hiểu đơn giản thì shell là công cụ để tương tác với môi trường Command Line Interface (CLI). Nói cách khác, các chương trình bash hoặc sh trong Linux và cmd.exe và Powershell trên Windows đều là các ví dụ về shell. Khi tấn công các hệ thống từ xa, đôi khi có thể ép một ứng dụng đang chạy trên máy chủ (ví dụ như một máy chủ web) để thực thi mã nguồn tùy ý. Khi điều này xảy ra, chúng ta muốn sử dụng quyền truy cập ban đầu này để có được shell chạy trên mục tiêu.

Đơn giản là, chúng ta có thể ép buộc máy chủ từ xa gửi cho chúng ta quyền truy cập dòng lệnh vào máy chủ (reverse shell), hoặc mở cổng trên máy chủ mà chúng ta có thể kết nối để thực thi các lệnh tiếp theo (bind shell).


## Tools

Có rất nhiều loại tool khác nhau được sử dụng để nhận một kết nối một reverse shell và gửi kết nối bind shell. Tổng thể, ta cần mã malicious shell cùng với cách tương tác với resulting shell. Resulting shell ở đây là shell mà chúng ta đã tạo ra và truy cập được sau khi tấn công thành công một hệ thống bằng mã malicious shell. Ta sẽ nói chi tiết hơn ở dưới đây: 

### reverse shells and bind shells

Reverse shells là khi mục tiêu bị buộc phải thực thi mã kết nối lại với máy tính của ta. Bằng việc sử dụng netcat hoặc socat để thiết lập một trình nghe được sử dụng để kết nối. Reverse shell là 1 phương án để bypass các quy tắc tưởng lửa ngăn chặn ta kết nối đến các cổng tùy ý trên mục tiêu. Ở cái này thì ta sẽ phải cấu hình mạng một chút, thông thường thì mình làm qua ngrok.

Bind shells là khi code đã thực thi trên mục tiêu được sử dụng để bắt đầu một trình nghe đưuọc gắn liền với một shell trực tiếp trên mục tiêu. Sau đó trình nghe sẽ được mở ra cho internet, nghĩa là ta có thể kết nối đến cổng mà mã đã mở và thực thi rce. Cái này thì ta sẽ không phải cấu hình gì trên mạng của mình nhưng mà lại dễ bị ngăn chặn bởi tường lửa của mục tiêu. 

Ví dụ cụ thể thì chẳng hạn một hacker muốn xâm nhập vào hệ thống mạng của một công ty, trong đó có một máy chủ web đang chạy trên cổng 80 và máy tính của hacker đang chạy trên địa chỉ ip công cộng lầ 123.45.67.89 chẳng hạn, thì để tấn công vào máy chủ web, hacker có thể sử dụng một lỗ hổng bảo mật trên máy chủ để tải xuống và thực thi mã độc, cho phép hacker thực hiện các lệnh trên máy chủ. Tuy nhiên để tạo một kết nối đến máy chủ hacker, hai phương án được thực thi ở đây sẽ là reverse shell và bind shell. Nếu hacker sử dụng revershell, mã độc sẽ bắt máy chủ web kết nối đến máy tính của hacker tại địa chỉ ip public 123.45.67.89 và cổng được chỉ định bởi hacker ví dụ 1234 đ biết phải không nhưng



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


**SUID**: viết tắt của set user id. Khi một tệp có quyền SUID được thực thi, nó được thực thi với đặc quyền của chủ sở hữu của nó thay vì người dùng thực thi nó. Ví dụ, nếu một tệp có quyền SUID được sở hữu bởi người dùng "root" và được thực thi bởi một người dùng khác, nó sẽ chạy với đặc quyền "root" thay vì đặc quyền của người dùng thực thi nó.
Ta cũng có thể dùng lệnh find để tìm kiếm các file này: **find / -perm -u=s -type f 2>/dev/null**
Trong đó thì -u=s ở đây sẽ tìm kiếm quyền SUID được thiết lập cho người sở hữu của tập tin, và ta có thể dùng nó để leo thang đặc quyền. 
