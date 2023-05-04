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
