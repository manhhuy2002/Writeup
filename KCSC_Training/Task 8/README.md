xss sql injection logic business authentication xml 

# xxe - xml external entity

## xxe vul là gì?

## xml external entity(xxe) vul xảy ra khi một ứng dụng web hoặt api chấp nhận dữ liệu xml đầu vào chưa được làm sạch và phần xử lí bên backend xml parser được cấu hình cho phép external xml entity parsing. ( là quá trình sử dụng các thực thể external xml trong quá trình phân tích tài liệu xml, khi một tài liệu xml được phân tích, các xml entity có thể được sử dụng để giải ãm các tham chiếu đến tài liệu hoặc dữ liệu external)
## DTDs và xml entities

- Trước khi một xml parser có thể xử lí xml input, ta cần khai báo cấu trúc của tài liệu đầu vào hợp lệ. 
- Trong tài liệu xml, các thực thể xml là các tham số đại diện cho các ký tự khó nhập hoặc đặc biệt. Các entities được định nghĩa trong 1 DTD (document type definition) bằng các sử dụng phần tử <!ENTITY>. Để tham chiếu đến một thực thể đã được định nghĩa, ta sử dụng tên của nó kèm theo dấu & ở trước và dấu ; ở đằng sau. Ví dụ nếu ta định nghĩa một thực thể có tên là "logo" trong DTD, thì để sử dụng thực thể này, ta có thể sử dụng &logo; . Khi tài liệu xml được phân tích, các thực thể sẽ được thay thế bằng các giá trị thực tế tương ứng của chúng.  
Tóm lại các thực thể xml là các tham số đại diện cho các ký tự khó nhập hoặc có ý nghĩa đặc biệt và được sử dụng để giúp cho việc tạo tài liệu xml trở nên dễ dàng hơn.



## Các phương thức leo thang đặc quyền trên linux

Kĩ thuật 1: từ user thấp leo thang đặc quyền lên user có đặc quyền cao, khi user được nằm trong 
một root đặc biệt như docker

Đầu tiên ta tạo 1 user có đặc quyền thấp và sau đó cho user này nằm trong nhóm docker và bắt đầu tiến hành leo thang đặc quyền lên:

Chuẩn bị: 

```
apt install docker.io

```

![image](https://user-images.githubusercontent.com/104350480/236109979-63cb7e23-b73f-4e86-958b-1a9bdfc30852.png)

Sau đó ta thêm 1 user với cmd: useradd -m user1

![image](https://user-images.githubusercontent.com/104350480/236112189-52c14929-0175-4f11-8673-9be3e7aaff8b.png)

![image](https://user-images.githubusercontent.com/104350480/236112283-ba778ab5-97fc-403d-8cda-7940b18baf81.png)


Từ đây ta sẽ cố gắng leo lên quyền root. Đối với môi trường máy host thì user1 không có  quyền hạn gì, nhưng khi chạy 1 container bất kì thì user này có khả năng mount dữ liệu từ máy host sang 1 container, mà trong 1 container thì user1 lại có toàn quyền. 
Ta sẽ tạo một container chạy hệ điều hành alpine: docker -it -v /:/linux alpine , -it interactive terminal cho phép ta tương tác với container qua terminal, -v /:/linux, tham số này  chỉ định một kết nối giữa thư mục bên máy host và thư mục linux trong container, ta để : là lấy tất. Sau khi chạy xong ta sẽ được như sau:

![image](https://user-images.githubusercontent.com/104350480/236121937-abb2a65e-2e69-47ca-aea8-a7d271423091.png)

Vói hostname là containerid. vào thư mục linux ta sẽ thấy tất cả các dữ liệu đã được mount từ root sang, giờ ta sẽ có vai trò như root và có thể đọc cũng như chỉnh sửa bất kì dữ liệu gì: 

![image](https://user-images.githubusercontent.com/104350480/236122163-6a2c108b-6e56-4a0d-94b3-03e89e5b00c6.png)

Leo thang đặc quyền ở đây chỉ là việc ta sẽ sửa đổi file /etc/shadow 1 chút, sửa phần pass của root để thành trắng và ta sẽ có thể chuyển sang root mà kh cần mật khẩu từ 1 user bất kì: 

![image](https://user-images.githubusercontent.com/104350480/236122317-d2d75bd5-8d3c-424c-b7e8-5e2d33f3247b.png)

Giờ thoát ra khỏi container và về user1 ban đầu:

![image](https://user-images.githubusercontent.com/104350480/236122473-45f9eec6-b576-409b-9cbb-6109a577061e.png)

Ta sẽ leo thang thành công mà không cần nhập mật khẩu. 


## 








