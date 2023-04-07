# Authentication vulnerabilities

## What is authentication? 
Xác thực là một quá trình xác minh định danh của người dùng hoặc khách hàng. Nói cách khác nó liên quan tới việc xác nhận chúng ta là ai. Có thể chia ra làm 3 tác nhân xác thực: 
+ Something you know, chẳng hạn như một mật khẩu hoặc một câu hỏi bảo mật. Cái này thường được gọi là "knowledge factors".
+ Somthing you have, là những đối tượng như điện thoại hoặc mã thông báo bảo mật. Hay còn được gọi là "possession factors". 
+ Somthing you are or do, ví dụ là dấu vân tay của những mẫu hành vi của ta. Thường được gọi là "inherence factors" (những nhân tố về đặc điểm sinh học). 

Những cơ chế xác thực dựa trên một loạt các kĩ thuật để xác minh một hoặc nhiều các tác nhân trên.

Điểm khác nhau giữa authen và autho là: 
+ Authen thì nó là một quá trình xác minh một người dùng và hiểu đơn giản là nó sẽ dùng để trả lời cho câu giỏi: Who are you? 
+ Autho thì cũng dùng để xác minh người dùng nhưng sẽ liên quan tới câu hỏi: what are you allowed to do?

Theo quy trình thực hiện thì ở đây, authen sẽ xác định rằng một người đang cố gắng truy cập vào một trang web thông qua đăng nhập xem người đó có phải là người trong hệ thống mà đã tạo tài khoản đó và mật khẩu hay không. Một khi được xác thực xong thì quyền của anh ta xác định liệu anh ta có được ủy quyền hay không, ví dụ, để truy cập thông tin cá nhân về người dùng khác hoặc thực hiện các hành động như xóa tài khoản của người dùng khác. 

## Những lỗ hổng xác thực sinh ra như thế nào?
Nhìn chung thì hầu hết các lỗ hổng trong cơ chế xác thực phát sinh theo 2 cách: 
- Những cơ chế xác thực yếu bởi chúng thất bại trong việc thực hiện bảo vệ đầy đủ trước những cuộc tấn công vét cạn.
- Những lỗ hổng logic hoặc là việc code kém trong việc triển khai các cơ thế xác thực khiến cho chúng có thể bị vượt qua bởi những kẻ tấn công. Cái này thường được gọi là "broken authentication" 
- 
