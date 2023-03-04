# Local file inclusion - remote file inclusion - path travesal

## 1. What is file inclusion?

Trong 1 số trường hợp, ứng dụng web được ta tạo ra cho phép ta truy cập các tệp nhất định trong hệ thống, bao gồm hình ảnh, văn bản tĩnh ,... thông qua 
các param, các param này là chuỗi truy vấn được truyền trên url có thể được sử dụng để truy xuất dữ liệu, hoặc thực hiện các hành động dựa trên đầu vào
của người dùng. 

File inclusion vul cho phép kẻ tấn công có thể xem các tệp trên máy chủ từ xa mà không cần nhìn thấy hoặc có thể thực thi các mã vào một mục tiêu bất kì trên 
trang web. File inclusion vuls thường được tìm thấy và khai thác trong các ngôn ngữ lập trình khác nhau cho các ứng dụng web, chẳng hạn như là php
được tạo và triển khai kém. Vấn đề chính ở đây là các xác thực đầu vào thường quá tin tưởng người dùng, vì vậy mà kẻ tấn công có thể kiểm soát chúng và thực hiện
các hành vi xâm phạm. Chẳng hạn cụ thể như lập trình viên sử dụng các lệnh include, require, include_once, require_once, ... các lệnh này cho phép việc file
hiện tại có thể gọi ra một file khác.

_ Dấu hiệu để nhận biết trang web có thể tấn công file inclusion là đường link thường có dạng php?page hoặc php?file=...., ngoài ra việc các thông tin nhạy cảm
của trang web bị tiết lộ.

## 2. Các kiểu tấn công

#### 2.1: Path Traversal

_ Hay còn được biết đến với tên Directory traversal, đây là 1 lỗ hổng bảo mật cho phép kẻ tấn công đọc được tài nguyện hệ thống, như các file local trên máy chủ 
đang chạy ứng dụng. Kẻ tấn công có thể khai thác lỗ hổng này bằng cách thao túng và lạm dụng url của ứng dụng web để định vị và truy cập các tệp hoặc thư mục được
lưu trữ bên ngoài thư mục gốc của ứng dụng.
__ Lỗ hổng này xủa ra khi đầu vào của người dùng được chuyển đến một hàm chẳng hạn như file_get_contents trong php. Điều quan trọng ta cần nhớ ở đây là đây không phải
nguyên nhân chính gây ra lỗ hổng web, mà là do việc xác thực hoặc lọc đầu vào kém là nguyên nhân chính gây ra lỗ hổng.
Trong php, ta có thể dùng file_get_contents để đọc nội dung của tệp cho phép ta đọc nội dung của 1 tệp hoặc 1 url và trả về cho ta kết quả dưới dạng chuỗi, cái này cũng khá
hay và được ứng dụng trong bài lab mà mình sẽ demo sau.

__ Biểu đồ sau đây cho ta thấy cách ứng dụng web lưu trữ tệp trong /var/www/app:

![image](https://user-images.githubusercontent.com/104350480/222916522-91a64f03-957e-41dd-850f-bda8833380a5.png)

Như biểu đồ trên vốn dĩ yêu cầu của người dùng sẽ phải là nội dung của userCV.pdf từ một đường dẫn đã được xác định sẵn là /var/www/app/CVs. Nhưng ở đây, kẻ
tấn công có thể lợi dụng điều này và tấn công path traversal, cũng được gọi với cái tên khác là tấn công dot-dot-slash, lợi dụng việc di chuyển thư mục 
bằng cách sử dụng ../ , nếu kẻ tấn công có thể tìm thấy điểm vào như trong trường hợp này là **get.php?file=** thì kẻ tấn công có thể gửi nội dung vào đó
như sau, **http://webapp.thm/get.php?file=../../.. /../etc/passwd**

Lỗi ở đây chính là không có xác thực đầu vào và không có biện pháp để ngăn chặn, và thay vì đáng ra phải truy cập vào /var/www/app/CVs thì kẻ tấn công đã
có thể truy xuất tệp từ các thư mục khác cụ thể ở đây là /etc/passwd. Mỗi .. di chuyển thư mục cho đến khi nó đến thư mục gốc /. Sau đó nó thay đổi thư mục
thành /etc và từ đó đọc tệp /passwd.

![image](https://user-images.githubusercontent.com/104350480/222917049-4aff73fb-6c9f-49b2-acbc-ed2829ec018a.png)

Do đó ứng dụng ở đây sẽ gửi lại nội dung tệp cho người dùng:

![image](https://user-images.githubusercontent.com/104350480/222917098-4b8f42ca-aa8d-4762-96f7-4fe57f308ffa.png)

Tương tự nếu ứng dụng web chạy trên máy chủ windows, kẻ tấn công có thể cung cấp đường dẫn windows. Ví dụ kẻ tấn công muốn đọc tệp boot.ini nằm trong C:\boot.ini
thì kẻ tấn công có thể thử một vài cách sau đây: 

```
http://webapp.thm/get.php?file=../../../../boot.ini 

http://webapp.thm/get.php?file=../../../../windows/win.ini

```
Điều này cũng tương tự với hệ điều hành linux.

Đôi khi, dev sẽ filter 1 số truy cập chỉ với 1 số file hoặc thư mục nhất đinh, dưới đây là 1 số file phổ biến ta có thể tham khảo:
- /etc/passwd: có tất cả người dùng đã đăng kí có quyền truy cập vào hệ thống.
- /etc/shadow: chứa thông tin về mật khẩu của người dùng trong hệ thống.
- /root/.bash_history: chứa các lệnh đã được thực thi của root
- /proc/version: chứa thông tin về phiên bản kernel của hệ thống

___ 
What function causes path traversal vulnerabilities in PHP?  file_get_contents
___


#### 2.2 Local file inclusion

__ Local file inclusion (lfi) là một kĩ thuật đọc file trong hệ thống, lỗi này xảy ra thường sẽ khiến website bị lộ các thông tin nhạy cảm, như là passwd,
php.ini, access_log,config.php,.... lỗi này xảy ra thường do dev thiếu kiến thức về bảo mật. Như với php, việc sử dụng các chức năng như include, require, 
include_once, require_once, .. thường khiến ứng dụng dễ bị tấn công. Chúng ta sẽ làm quen với lỗ hổng này bằng ngôn ngữ php trước, mấy ngôn ngữ khác thì thôi
để sau vậy. Khai thác lfi thì nó tuân theo các khái niệm mà đã được đề cập ở path travesal đã nói ở phần trước.

Giả sử ứng dụng web cung cấp hai ngôn ngữ và người dùng có thể chọn giữa EN và VN:

![image](https://user-images.githubusercontent.com/104350480/222918152-0322c108-1f73-48c4-b851-7a06239343e8.png)

Code php ở trên sử dụng yêu cầu GET thông qua tham số url lang để đọc và thực thi tệp của trang bằng include, cuộc tấn công có thể được gửi bằng các yêu cầu
http như sau: http://webapp.thm/index.php?lang=EN.php để tải trang tiếng anh hoặc http://webapp.thm/index.php?lang=VN.php để tải trang tiếng việt, trong đó
các tệp EN.php và VN.php tồn tại trong cùng 1 thư mục.

Về mặt lí thuyết thì ta có thể truy cập và hiển thị bất kì file nào trên máy chủ từ đoạn code ở trên nếu không có xác thực đầu vào. Giả sử ta muốn đọc tệp
/etc/passwd chứa thông tin nhạy cảm về người dùng trên hđh linux, chúng ta có thể sử dụng cách sau: **http://webapp.thm/get.php?file=/etc/passwd**

__ Tiếp theo, trong đoạn mã sau, dev đã chỉ định thư mục bên trong hàm include: 

![image](https://user-images.githubusercontent.com/104350480/222918508-97956f07-742b-45c4-9ce0-42daa52c040b.png)

Tức nếu ta truyền như trên thì sẽ không có hiệu quả, vì nó đã được chỉ định trong thư mục languages chỉ thông qua tham số lang.

Nếu không có xác thực đầu vào thì cách trên cũng không có hiệu quả lắm vì ta hoàn toàn có thể áp dụng path traversal ở trên để tấn công, chẳng hạn, đường
dẫn bây giờ sẽ trở thành ../../../../etc/passwd

__ Trong 2 trường hợp đầu thì ta có source code nên ta có thể khai thác nó luôn. Còn tiếp theo ta sẽ đến với việc thực hiện kiểm tra black box testing, khi 
không có source code, bắt đầu với trường hợp lỗi trả về trực tiếp trên trang web khi ta thực hiện truy vấn sai, chẳng hạn nhập 'THM' chẳng hạn, sẽ có lỗi
trả về như sau: 

![image](https://user-images.githubusercontent.com/104350480/222918822-69af4395-32f1-481b-b97b-17523a73ffd9.png)


Thông báo lỗi đã tiết lộ thông tin quan trọng. Bằng cách nhập THM làm đầu vào hay bất cứ cái gì khác mà kh phải là EN hay VN thì nó đã trả về lỗi cho ta là
nó đang được thực hiện trong 




