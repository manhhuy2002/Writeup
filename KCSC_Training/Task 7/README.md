## JerseyCTF

### put-the-cookie-down

![image](https://user-images.githubusercontent.com/104350480/232238888-c18e6771-1095-41a5-bcb5-cc02dc7a582f.png)

> FLag: I_WILL_BE_BACK_FOR_MORE_C00KI3S!

### look-im-hacking

![image](https://user-images.githubusercontent.com/104350480/232239086-e3e3e07f-93ce-435e-84d4-d167a3eea92c.png)

Ctrl U có: 

![image](https://user-images.githubusercontent.com/104350480/232239104-5a13832c-bf05-4fc5-bb3a-fbe2a459007e.png)

Flag: 

![image](https://user-images.githubusercontent.com/104350480/232239134-7e9f5b12-2d44-4b81-9444-9906f365ed4f.png)

### [i-got-the-keys](https://ctf.jerseyctf.com/challenges#i-got-the-keys-35)

![image](https://user-images.githubusercontent.com/104350480/232287618-3e4cf226-d0da-4011-af37-fbc2f81adb47.png)

Ctrl U ta được: 

![image](https://user-images.githubusercontent.com/104350480/232287630-3844f786-744d-439e-85ef-3c5a8e9913e1.png)

Ta có endpoint test, với điều kiện phải có headers: {
        'authorization_key': 'GdERHpBh3x'
      }
 Nếu không ta sẽ có lỗi như sau:
 
 ![image](https://user-images.githubusercontent.com/104350480/232289946-0766ef4e-a61d-4d9f-a908-80ec27c179dc.png)

Ném qua burpsuite thêm authorization_key: GdERHpBh3x vào header ta sẽ được: 

![image](https://user-images.githubusercontent.com/104350480/232290166-e86b8b2e-4a16-410f-a254-6f5eac7b04f0.png)

Nhưng như vậy thì cũng kh khai thác được gì, ở đây fuff thử trang web xem có đường dẫn nào khác không: 

![image](https://user-images.githubusercontent.com/104350480/232290289-d295c1fc-683c-49ab-8ea1-bb6c552bfbce.png)

Có /flag trả về với lỗi 500, nghĩa là có thể nó bị khóa truy cập như việc thiếu Authorization_key: GdERHpBh3x ở header:

Thay vào burpsuite ta get được flag trả về:

![image](https://user-images.githubusercontent.com/104350480/232290412-4284d9d9-487a-4be8-a20f-d276b88dfdef.png)

> Flag: jctf{*MAJ0R-K3Y-AL3RT*}

### ninja-jackers

![image](https://user-images.githubusercontent.com/104350480/232291689-ff7fee7f-1c66-4b00-a5b8-643e9cf8e296.png)

Truy cập vào trang ta được:

![image](https://user-images.githubusercontent.com/104350480/232291763-0fbb7bec-637b-4342-a0ae-fde96c345f2a.png)

Sang phần /contact: 

![image](https://user-images.githubusercontent.com/104350480/232291697-e28e682b-db0d-4472-8e56-869a9b0d73bc.png)

Ta thấy thông báo /contact được trả về, nghĩ đến khả năng đây là ssti, ta thử \{{7\*7}} xem và được: 

![image](https://user-images.githubusercontent.com/104350480/232292156-30cbba98-3c19-4acf-ba8d-adba1b8cd3b8.png)

Vậy là nó dính SSTI rồi, giờ ta thử tiếp để xem đây là template gì, thử {{7\*'7'}}:

![image](https://user-images.githubusercontent.com/104350480/232292404-8c8a316a-bd7d-41e5-917c-a8303109b34e.png)

Cả 2 đều không lỗi ta có thể kết luận tempalate này là jinja2, giờ hoặc tự tạo builtins rồi import os và dùng cmd hoặc dùng luôn hàm sẵn đều được: 

![image](https://user-images.githubusercontent.com/104350480/232293106-b378c27f-0d72-4523-bd9e-f4abc9457382.png)

![image](https://user-images.githubusercontent.com/104350480/232293265-610b9fe0-3f93-4cdc-b349-30580a5d286e.png)

Ta được file FLAG_is_H3RE.txt, giờ cat file ra là ta có flag: 

![image](https://user-images.githubusercontent.com/104350480/232293382-4518b3e0-c046-452c-97dd-218ba340fe4b.png)

> Flag: jctf{Ar3Nt_Y0U_GLaD_I_Didnt_SAY_NINJA}







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
