## Trần Mạnh Huy - Các chall về lfi - rfi - path traversal - File upload vulnerabilities

* [Chết.vn](#chetvn)
* [Portswigger - File Path Traversal](#Portswigger---file-path-traversal)
* [Portswigger -File upload vulnerabilities](Portswigger---File-upload-vulnerabilites)
* [Root me - Upload file](#root-me---upload-file)
* [Root me]()
* [Tryhackme - dogcat](#tryhackme---dogcat)

## Chết.vn

Mình viết nốt các chall tuần trước chưa làm được.

### Chall 5:

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

### Chall 7: 

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

### Chall 10: 

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

## Portswigger - File Path Traversal

### 1. Lab: File path traversal, simple case

Chall giới thiệu về file path traversal, load trang web và thấy đường dẫn ảnh với param là filename, chuyển thành ../../../etc/passwd để truy cập là xong: 

![image](https://user-images.githubusercontent.com/104350480/222962685-9ceb4899-1e03-453e-865a-324a28f530b8.png)

### 2. Lab: File path traversal, traversal sequences blocked with absolute path bypass

Cái này chỉ cần chuyển thành filename=/etc/passwd là xong, vì cái này là do trang web nó block ../ nhưng mà lại coi tên tệp được cung cấp như 1 thư mục làm việc mặc định:

![image](https://user-images.githubusercontent.com/104350480/222962841-b4810a0f-911f-400a-aa12-97a8ceab53df.png)

### 3. Lab: File path traversal, traversal sequences stripped non-recursively

Lab này chỉ đơn giản là ta test vài trường hợp như kiến thức đã học thì nó thuộc dạng filter nhưng kh đệ quy:

![image](https://user-images.githubusercontent.com/104350480/222963091-a38cfb4a-303a-4b6e-b49d-1a22f859c52c.png)

### 4. Lab: File path traversal, traversal sequences stripped with superfluous URL-decode

Bài này thì nó lọc đi / bằng cách decode url ra rồi nếu có thì lọc, thì ta encode 2 lần thì url sau khi deocde ra vẫn sẽ được hiểu:

![image](https://user-images.githubusercontent.com/104350480/222963210-a20b40b0-adec-4e4b-ab08-936f27612eb4.png)

### 5. Lab: File path traversal, validation of start of path

Nhìn qua ta cũng biết được dạng này nó giống như trong tài liệu đã đọc, nó thuộc dạng yêu cầu đường dẫn phải có sẵn, như trong bài đọc là file languages được mặc định yêu 
cầu thì ở đây cũng vậy /var/www/images cũng phải có kh thì sẽ bị báo lỗi. Ta để nguyên rồi thêm ../ đủ  thì thôi là được: 

![image](https://user-images.githubusercontent.com/104350480/222963342-88597dff-d5cc-4b03-b96c-0c05e555f6d8.png)

### 6. Lab: File path traversal, validation of file extension with null byte bypass

Đọc tên đề là biết dạng null byte rồi, test ../../../etc/passwd%00 nhưng trả về no file, thử tiếp thì cũng kh được, có vẻ là nó kh phải thêm file mà là phải dạng jpg thì
nó mới chấp nhận, vậy bypass thì thêm đuôi jpg vào sau null byte là được, đằng nào cũng mất:

![image](https://user-images.githubusercontent.com/104350480/222964011-a29abf86-afa5-4df6-879c-0c7c8f60dbb0.png)

## Portswigger -File upload vulnerabilities

### 1. Lab: Remote code execution via web shell upload

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

### 2. Lab: Web shell upload via Content-Type restriction bypass

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


### 3. Lab: Web shell upload via path traversal

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


### 4. Lab: Web shell upload via extension blacklist bypass

```
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

```

## Root me - Upload file

Mấy chall này chủ yếu dùng kiến thức trong related source được rootme cấp sẵn:

> link reference: https://repository.root-me.org/Exploitation%20-%20Web/EN%20-%20Secure%20file%20upload%20in%20PHP%20web%20applications.pdf?_gl=1*vt6jw*_ga*NzM2OTE5OTkuMTY2OTA4MzExMA..*_ga_SRYSKX09J7*MTY3ODAzOTcxMy4xNDQuMS4xNjc4MDM5NzE4LjAuMC4w

### Chall 1: File upload - Double extensions

```
Your goal is to hack this photo galery by uploading PHP code.
Retrieve the validation password in the file .passwd at the root of the application.

```

![image](https://user-images.githubusercontent.com/104350480/223013207-fe95da40-dd81-4445-bf53-eeb9ec5886ea.png)

Chỉ được ** .gif, .jpeg , .png** được chấp nhận nên ta sửa lại đuôi, thêm .png vào file php là được.
Ta gửi file cái sang phần get đọc được luôn, vì vậy mà nó có tên double extension.

![image](https://user-images.githubusercontent.com/104350480/223013520-c3794348-52fc-485f-ab36-d07dcc27ee22.png)

Dùng find để tìm find .passwd: 

![image](https://user-images.githubusercontent.com/104350480/223016249-1df432b5-35a5-4aec-b7ce-275d35bd5a85.png)

Dùng cat để đọc file: 

![image](https://user-images.githubusercontent.com/104350480/223016429-029c6297-dc3b-4796-8ef4-bfd7ca8398d7.png)

> Flag: Gg9LRz-hWSxqqUKd77-_q-6G8

### Chall 2: File upload - MIME type

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

### Chall 3: File upload - Null byte

```
Your goal is to hack this photo galery by uploading PHP code.

```

Bài này khá đơn giản, cũng tương tự như các chall trước, ta chỉ được up các file: **NB : only GIF, JPEG or PNG are accepted**, nếu như ý tưởng là thêm đuôi .png vào thì kh
được bài này nó đã filter double extension rồi, nên khi truyền lên với .php.png thì cũng fail, vậy ta chỉ cần thêm %00 vào sau như: .php%00.png vào là xong, vì sau khi thực 
hiện .png sẽ tự động bị cho out ra khỏi. còn lại mỗi extension là .php. Sau đó ta thực hiện đọc file và được password trả về luôn:

![image](https://user-images.githubusercontent.com/104350480/223034178-74c4ccdd-0756-4db0-a5d1-a9d58708a2d0.png)

> Flag: YPNchi2NmTwygr2dgCCF

### Chall 4: File upload - ZIP

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

### Chall 5: File upload - Polyglot

#### Are you bilingual?

```
Your friend who is a photography fan has created a site to allow people to share their beautiful photos. He assures you that his site is secure because he checks that the file sent is a JPEG, and that it is not a disguised PHP file. Prove him wrong

```
Gợi ý rất rõ ràng, bạn là người song ngữ ? Chall này thì cũng giống với chall polyglot của portswigger, có lẽ ta cần phải upload 2 file
