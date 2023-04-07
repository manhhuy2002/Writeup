# Authentication vulnerabilities

## What is authentication? 
Xác thực là một quá trình xác minh định danh của người dùng hoặc khách hàng. Nói cách khác nó liên quan tới việc xác nhận chúng ta là ai. Có thể chia ra làm 3 tác nhân xác thực: 
+ Something you know, chẳng hạn như một mật khẩu hoặc một câu hỏi bảo mật. Cái này thường được gọi là "knowledge factors".
+ Somthing you have, là những đối tượng như điện thoại hoặc mã thông báo bảo mật. Hay còn được gọi là "possession factors". 
+ Somthing you are or do, ví dụ là dấu vân tay của những mẫu hành vi của ta. Thường được gọi là "inherence factors" (những nhân tố về đặc điểm sinh học). 

Những cơ chế xác thực dựa trên một loạt các kĩ thuật để xác minh một hoặc nhiều các tác nhân trên.

Theo quy trình thực hiện thì ở đây, authen sẽ xác định rằng một người đang cố gắng truy cập vào một trang web thông qua đăng nhập xem người đó có phải là người trong hệ thống mà đã tạo tài khoản đó và mật khẩu hay không. Một khi được xác thực xong thì quyền của anh ta xác định liệu anh ta có được ủy quyền hay không, ví dụ, để truy cập thông tin cá nhân về người dùng khác hoặc thực hiện các hành động như xóa tài khoản của người dùng khác. 

Cụ thể hơn thì authentication trong ứng dụng web có thể xuất hiện ở các lớp nghĩa khác nhau: chẳng hạn việc server xác thực yêu cầu do đúng từ client của người dùng đó gửi lên hay không cũng là một quá trình authentication, ở đây server có thể dùng các phương thức xác thực như authentication token, session, digital siganture, hoặc việc mã hóa để kiểm tra và đảm báo tính xác thực của yêu cầu được gửi từ client đó. Cũng trong đó, việc server xác thực nội dung yêu cầu do đúng từ người dùng hợp lệ gửi lên hay không cũng là authentication. 

Về bản chất thì khi ta gửi một http request tới một trang web để lấy nội dung hoặc thực hiện các hành vi trên trang web, thì làm sao để server biết ta là ai. Trong đó thì http request là một stateless protocol, tức là các request sẽ được xử lí một cách độc lập, không phục thuộc vào trạng thái hay kết quả của các request trước. Và để giải quyết vấn đề này, cũng như có thể xác thực được những yêu cầu này đến từ ai, thì http sử dụng các phiên (session) hoặc mã thông báo (authentication token) để duy trì thông tin về trạng thái của client giữa các yêu cầu. Cụ thể như cookie hoặc jwt,...
Minh hoạt cho việc stateless của http request: 

![image](https://user-images.githubusercontent.com/104350480/230588126-591ba14d-5f5b-4e1d-a369-7e737f647449.png)

Và muốn xác nhận các http request được gửi từ một người nào đó: 

![image](https://user-images.githubusercontent.com/104350480/230588283-5327bae9-7b5f-4f29-9c56-f4460ffe3cea.png)


## Điểm khác nhau giữa authentication và authorization
+ Authen thì nó là một quá trình xác minh một người dùng hoặc một hành dộng nào đó và hiểu đơn giản là nó sẽ dùng để trả lời cho câu giỏi: Who are you? 
+ Authorization thì cũng dùng để xác minh người dùng nhưng sẽ liên quan tới câu hỏi: what are you allowed to do? Nó được sinh ra sau khi xác thực thành công, đây sẽ là việc kiểm soát các truy cập theo ngang, dọc và cả theo ngữ cảnh. 

## Những lỗ hổng xác thực sinh ra như thế nào?
Nhìn chung thì hầu hết các lỗ hổng trong cơ chế xác thực phát sinh theo 2 cách: 
- Những cơ chế xác thực yếu bởi chúng thất bại trong việc thực hiện bảo vệ đầy đủ trước những cuộc tấn công vét cạn.
- Những lỗ hổng logic hoặc là việc code kém trong việc triển khai các cơ thế xác thực khiến cho chúng có thể bị vượt qua bởi những kẻ tấn công. Cái này thường được gọi là "broken authentication" 
Trong đó thì có thể chia ra làm 3 loại chính: 
- Lỗ hổng trong đăng nhập dựa trên mật khẩu.
- Lỗ hổng trong xác thực đa yếu tố.
- Lỗ hổng trong các cơ chế xác thực khác.

## Lỗ hổng trong đăng nhập dựa trên mật khẩu: 
