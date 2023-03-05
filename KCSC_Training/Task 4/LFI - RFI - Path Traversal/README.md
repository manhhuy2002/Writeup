## Trần Mạnh Huy - Các chall về lfi - rfi - path traversal - File upload vulnerabilities
* [Portswigger - File Path Traversal](#Portswigger---file-path-traversal)
* [Portswigger -File upload vulnerabilities](Portswigger---File-upload-vulnerabilites)
* [Root me]()
* [Tryhackme - dogcat](#tryhackme---dogcat)

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

Vậy là up lên thành công rồi
