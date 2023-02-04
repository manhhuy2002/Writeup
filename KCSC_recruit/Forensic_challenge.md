# KCSC_CTF Writeup - Trần Mạnh Huy

## FORENSIC_CHALLENGE:
## Table of content forensic challenge:
* [Đề 1: Mail](#Đề-1-mail)
* [Đề 2: Window password](#Đề-2-window-password)



## Đề 1: Mail
### My friend, Anthony, is a wibu. Recently he received a suspicious mail. He backed up the email and sent it to me. Can you analyze that mail?
url: https://bcm.kcslab.asia/files/7aaa96c57d72c2628a477254cb8bb308/mail.mbox?token=eyJ1c2VyX2lkIjo0MiwidGVhbV9pZCI6bnVsbCwiZmlsZV9pZCI6Mn0.Y8ZUEA._JSa0tegwVT9kTfRvj2iTnz8yWY)

Đề bài nói rằng ng bạn Anthony của tác giả có nhận được 1 mail đáng ngờ, anh ấy sao lưu và chuyển nó đến cho tác giả. Em cùng mọi người phân tích mail này với tác giả nhé!!
```
Để phân tích  mail, em dùng 1 công cụ là MBOX VIEWER rất tiện ích để phân tích.
```
Em mở bức thư được giao diện như sau : 

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/mail1.jpg)



Đọc cẩn thận từng phần của lá thư, từ lịch sử của mail đến cả đường link vào youtube em kh thấy có gì bất thường, cho đến đoạn này thì có vẻ có điều gì đó đang được ẩn giấu ở đây:

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/mail2.jpg)


Rõ ràng file pdf đính kèm này có gì đó bất thường do những thứ khác đâu khai thác được gì, em mở ra ta được 1 file ảnh dưới dạng pdf được đính kém:

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/mail0.jpg)


Em nghĩ đến việc trích xuất file pdf này ra và ném vào linux để trích xuất thì khả năng sẽ thu được điều gì đấy. Vì vậy em thử phân tích file pdf bằng 1 số kĩ thuật phân tích cơ bản của linux như:
+ Exiftool(ExifTool là một thư viện Perl độc lập với nền tảng cộng với một ứng dụng dòng lệnh để đọc, viết và chỉnh sửa thông tin meta trong nhiều loại tệp)
+ Binwalk(Binwalk là một công cụ nhanh, dễ sử dụng để phân tích, kỹ thuật đảo ngược và trích xuất hình ảnh phần sụn)
+ Ngoài ra là 1 số cmd dơn giản như strings, cat | grep ... để tìm kiếm nhanh nội dung cần tìm như flag.
- Vì ta cần trích xuất ở bên trong tệp ảnh có gì nên sau khi đọc 'man exiftool' và 'man binwalk', em thấy bên binwalk có lệnh 'binwalk -e' để extract file ảnh.
- Nhưng chạy thử thì binwalk -e Freemaker.pdf thì có vẻ báo lỗi "binwalk itself must be run as root", vậy nên em sửa lại cmd thành: 
```
binwalk --run-as=root -e Freemaker.pdf
```
Và thế là extract thành công. Em thu được 1 thư mục mới '_Freeticket.pdf.extracted' Tiếp tục ta thực hiện từng bước như trong ảnh rồi kiểm tra bằng strings kết hợp grep tìm 'KCSC' xem có thu được gì kh. Ten ten, vậy là em có được flag của bài này.

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/mail3.jpg)


>Flag: KCSC{Anata_n0_baka_aho_aho_>///<}


## Đề 2: Window password

#### In Microsoft Windows, the file lsass.exe in the directory c:\windows\system32 or c:\winnt\system32 is the Local Security Authority Subsystem Service. It has the file description LSA shell. It is a crucial component of Microsoft Windows security policies, authority domain authentication, and Active Directory management on your computer.
Could you crack my password?

Từ đề bài thì em phân tích được cái mình cần quan tâm ở đây là file lsa shell, nó là thành phần quan trọng các chính sách bảo mật của window microsoft. Sau đó em download file được cho về linux bằng lệnh wget:

wget https://bcm.kcslab.asia/files/15903891bdafbd54d05a3c87d568c215/Windows_Password.zip?token=eyJ1c2VyX2lkIjo0MiwidGVhbV9pZCI6bnVsbCwiZmlsZV9pZCI6NX0.Y8ZdwA.hfdaUKNpdcOEmrzeqeTNrlqKdcA

#### Ta down về và unzip ra được 1 folder window_password chứa file lsass.dmp

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/pass1.jpg)

Điểm qua 1 chút về lsass:
+ lsass là quy trình trên Microsoft Windows xử lý tất cả xác thực người dùng, thay đổi mật khẩu, tạo mã thông báo truy cập và thực thi chính sách bảo mật. Và là quy trình lưu trữ này thường có dạng mật khẩu băm và trong một số trường hợp thậm chí còn lưu trữ mật khẩu người dùng ở dạng văn bản gốc.
+ Và để xử lí tệp lsass này em đã dùng PYPYKATZ để xử lí dump file này trên linux ( nếu trên window có thể dùng MIMIKATZ), với câu lệnh:

```
pypykatz lsa minidump lsass.DMP 
```

- Sau khi thực thi lệnh trên linux, ta trích xuất được thông tin từ file dump trên em được 1 list thông tin xác thực, kéo từ trên xuống rồi chú ý user KCSC

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/pass2.jpg)

Dùng haiti trên kali linux để phân tích đoạn encrypt NT: 

> c456c606a647ef44b646c44a227917a4

Sau đó dùng john the ripper để crack password, được mật khẩu:  Password@


>flag: KCSC{Password@}
