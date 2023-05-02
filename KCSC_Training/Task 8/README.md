xss sql injection logic business authentication xml 

# xxe - xml external entity

## xxe vul là gì?

## xml external entity(xxe) vul xảy ra khi một ứng dụng web hoặt api chấp nhận dữ liệu xml đầu vào chưa được làm sạch và phần xử lí bên backend xml parser được cấu hình cho phép external xml entity parsing. ( là quá trình sử dụng các thực thể external xml trong quá trình phân tích tài liệu xml, khi một tài liệu xml được phân tích, các xml entity có thể được sử dụng để giải ãm các tham chiếu đến tài liệu hoặc dữ liệu external)
## DTDs và xml entities

- Trước khi một xml parser có thể xử lí xml input, ta cần khai báo cấu trúc của tài liệu đầu vào hợp lệ. 
- Trong tài liệu xml, các thực thể xml là các tham số đại diện cho các ký tự khó nhập hoặc đặc biệt. Các entities được định nghĩa trong 1 DTD (document type definition) bằng các sử dụng phần tử <!ENTITY>. Để tham chiếu đến một thực thể đã được định nghĩa, ta sử dụng tên của nó kèm theo dấu & ở trước và dấu ; ở đằng sau. Ví dụ nếu ta định nghĩa một thực thể có tên là "logo" trong DTD, thì để sử dụng thực thể này, ta có thể sử dụng &logo; . Khi tài liệu xml được phân tích, các thực thể sẽ được thay thế bằng các giá trị thực tế tương ứng của chúng.  
Tóm lại các thực thể xml là các tham số đại diện cho các ký tự khó nhập hoặc có ý nghĩa đặc biệt và được sử dụng để giúp cho việc tạo tài liệu xml trở nên dễ dàng hơn.




