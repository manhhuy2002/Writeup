# Trần Mạnh Huy - Sql injection - Tìm hiểu 
<hr>

## Ở trong phần tìm hiểu này ta sẽ trả lời 5 câu hỏi:

* [1. What is Sqli attack?](#1-what-is-sqli-attack?)
* [2. How does Sqli attack work?](#2-how-does-sqli-attack-work?)
* [3. How many type of Sqli attack?](#3-how-many-type-of-sqli-attack?)
* [4. What is impact of Sqli attack?](#4-what-is-impact-of-sqli-attack?)
* [5. How to prevent Sqli attack?](#5-how-to-prevent-sqli-attack?)


## 1. What is Sqli attack?

Sqki là 1 lỗ hổng bảo mật web cho phép kẻ tấn công có thể can thiệp được vào câu lệnh truy vấn mà ứng dụng sử dụng để lấy dữ liệu. 
- Nó cho phép  kẻ tấn cong xem được dữ liệu nhạy cảm mà đáng ra là sẽ không được phép truy vấn vào. Trong nhiều trường hợp thì nó khiến cho phép kẻ tấn
công đọc, chỉnh sửa và xóa cả dữ liệu, gây ra những thay đổi và tác động xấu đến nội dung của ứng dụng.
- Trong một số trường hợp thì kẻ tấn công có thể leo thang tấn công sqli để xâm phạm máy chủ hoặc cơ sở hạ tầng hoặc biểu diễn 1 DOS.

## 2. How does sqli attack work

- Sqli hoạt động được là do dữ liệu đầu vào không được kiểm tra hoặc lọc kĩ lưỡng
- Sử dụng các thao tác sqli động, thao tác nối dữ liệu người dùng với các truy vấn sqli gốc.
-
## 3. How many type of sqli attack?

#### Nhìn chung ta có thể chia sqli thành 3 dạng sau: 

1. In-Band Sqli
- Error-based Sqli
- Union-based Sqli
2. Blind Sqli
- Blind - Boolean-based Sqli
- Blind- Time-based Sqli
3. Out-of-band Sqli

Ta sẽ đi vào chi tiết từng loại tấn công:

#### 1. In-Band Sqli

- Error-based slqi là một kỹ thuật tấn công sqlii dựa vào thông báo lỗi được trả về từ phía database server có chứa thông tin về cấu trúc cơ sở
dữ liệu. Từ đó ta lợi dụng nó để thực hiện tấn công.
- Union-based sqli là một kỹ thuật tấn công dựa vào việc kết hợp toán tử UNION trong ngôn ngữ sql cho phép ta tổng hợp kết quả của 2 hay nhiều câu
truy vấn lại với nhau và trả về như 1 phần của http response. Ta sẽ đi sâu hơn việc giải thích ở các bài lab.

#### 2. Blind Sqli

- Không giống như In-band sqli, thì Blind slqi tốn nhiều thời gianhown cho việc tấn công do không có bất kì dữ liệu nào được thực sự trả về thông qua 
việc truy vấn trên web va attacker kh thể theo dõi kết quả trực tiếp như kiểu tấn công In-band.
- Thay vào đó, kẻ tấn công sẽ cố gắng xây dựng câu lệnh truy vấn kết hợp với payload sao cho phù hợp để có thể trả về kết quả phản hồi và kết quả
hành vi của database server.
- Có 2 dạng tấn công chính ở đây:
  - Blind-boolean-based
  - Blind-tim-based

#### Blind-boolean-based
- Là một kĩ thuật tấn công sql injection dựa vào việc gửi các truy vấn tới cơ sở dữ liệu và bắt buộc ứng dụng trả về kết quả khác nhau phụ thuộc vào
True hoặc False.
- Tùy thuộc vào kết quả của câu truy vấn mà HTTP response sẽ có thể thay đổi hoặc giữ nguyên.
- Kiểu tấn công này chậm vì phải đi brute-force từng kí tự một.

#### Time-based blind:
- Time-base blind sqli là 1 kĩ thuật dựa vào việc gửi các truy vấn đến cơ sở dữ liệu và buộc cơ sở dữ liệu phải chờ 1 khoảng thời gian (nếu đúng) 
trước khi phản hồi.
- Dựa vào khoảng thời gian này, nếu sleep() được thì chứng tỏ ta đã thực hiện điều kiện đúng còn nếu sai thì ứng dụng sẽ phản hồi ngay lập tức. 
- Vì kiểu này ta cũng phải đi brute-force từng kí tự một nên cũng sẽ khá là mất thời gian như dạng blind-boolean-based

#### 3. Out-of-band sqli


