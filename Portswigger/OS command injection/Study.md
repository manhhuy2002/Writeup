# Command injection

#### Trong bài lab này chúng ta sẽ tìm hiểu về command injection. Khi chúng ta hiểu về vulnerability này thì sẽ nói sâu hơn về tác động cũng như rủi ro mà chúng
đem lại.

Sau đó ta sẽ đưa kiến thức này vào thực tế với những nội dung như sau:
- How to discover the command injection vulnerability.
- How to test and exploit this vulnerability usings payloads desinged for diffirent operatings systems
- How to prevent this vulnerability in an web application.
- And the last, you will apply theory to practice learning in a practical at the end of the room.

Ok triển khai nào?

Đầu tiên command injection là gì? Command injection là việc abuse hành vi của ứng dụng để execute những câu lệnh trên hệ thống vận hành, sử dụng same privileges 
mà các ứng dụng đang chạy. Cụ thể là nó cho phép kẻ tấn công chèn thêm các câu lệnh để thực thi, qua đó cho phép kẻ tấn công có thể kiểm soát và thực thi các 
lệnh trên máy chủ mà hệ thống ứng dụng đang chạy.
