# JerseyCTF

## [1. put-the-cookie-down](https://jerseyctf-put-the-cookie-down.chals.io/)

![image](https://user-images.githubusercontent.com/104350480/232238888-c18e6771-1095-41a5-bcb5-cc02dc7a582f.png)

> FLag: I_WILL_BE_BACK_FOR_MORE_C00KI3S!

### [2. look-im-hacking](https://www.jerseyctf.online/)

![image](https://user-images.githubusercontent.com/104350480/232239086-e3e3e07f-93ce-435e-84d4-d167a3eea92c.png)

Ctrl U có: 

![image](https://user-images.githubusercontent.com/104350480/232239104-5a13832c-bf05-4fc5-bb3a-fbe2a459007e.png)

Flag: 

![image](https://user-images.githubusercontent.com/104350480/232239134-7e9f5b12-2d44-4b81-9444-9906f365ed4f.png)

## [3. i-got-the-keys](https://ctf.jerseyctf.com/challenges#i-got-the-keys-35)

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

## [4. Timeless](http://137.184.135.40:3000/)

![image](https://user-images.githubusercontent.com/104350480/232410378-4ebe5bf4-2664-403a-b70f-fe6b15e14312.png)

Giao diện của bài:

![image](https://user-images.githubusercontent.com/104350480/232410122-74be4254-da53-4810-8bd3-c4a3190dfaef.png)

Thử sql injection đơn giản xem có bypass authen được không thì trả về lỗi: 

![image](https://user-images.githubusercontent.com/104350480/232410500-cb54bc5f-50c0-4cc5-bf07-0c405ed5fae0.png)

Tiếp tục thử ở password: 
```
username: admin
password: ' OR 1=1 -- -

```
Ta bypass được luôn, bài kh filter ở phần password: 

![image](https://user-images.githubusercontent.com/104350480/232410660-ad14eb2b-4fef-4234-80c2-d7e68db9a9c9.png)

> Flag: jctf{LOVE_ALL_TRUST_A_FEW_&_DO_WRONG_TO_NONE}


## [5. ninja-jackers](https://jerseyctf-ninja-jackers.chals.io/)

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

## [6. Poisoned](https://jerseyctf-poisoned.chals.io/?page=welcome)

![image](https://user-images.githubusercontent.com/104350480/232411154-3e2ba221-99fc-4159-932d-fda0b955fe18.png)

Giao diện của bài đang get dữ liệu qua tham số truy vấn page, ta thử thay giá trị thành a xem sao: 

![image](https://user-images.githubusercontent.com/104350480/232411976-e6a1583f-31d1-4540-b842-e9052618ab76.png)

Lỗi hiển thị đường dẫn hiển thị trên màn hình, đặc trưng của lfi và path traversal, ngoài ra nó đang ở trong thư mục pages, giờ ta sẽ thử đọc file hệ thống xem được không: 

![image](https://user-images.githubusercontent.com/104350480/232413381-6e35d0b4-5372-4f4f-a401-07a29c8fb009.png)

Bài có filter đi ../, nhưng ở đây bài không filter đệ quy, nó chỉ filter 1 lần, ta sửa thành ....// để tiếp tục:

![image](https://user-images.githubusercontent.com/104350480/232413910-7c2191b1-d124-4688-a511-085c412b079f.png)

Nhưng đọc được file hệ thống như thế này thì cũng kh giải quyết được gì vì ta cần biết chính xác vị trí của tệp flag được giấu trong bài, ở đây ta sẽ thực hiện inject qua vul log poisoning như tên đề đã gợi ý: 
Trước hết truy cập vào file log, ....//....//....//....//var/log/apache2/access.log ta được:

![image](https://user-images.githubusercontent.com/104350480/232415890-e4d7914a-3251-45ca-ab20-42a4b8203229.png)


Thông thường ta sẽ tiêm shell code thông qua user-agent như dưới để có thể thực thi được các lệnh: 

![image](https://user-images.githubusercontent.com/104350480/232416062-c135f743-adda-47b6-bce8-f89593f47343.png)

Nhưng có vẻ bài đã cho sẵn shell code nên giờ ta cần truyền nó qua tham số truy vấn poison là xong, thử với đường dẫn /?page=....//....//....//....//....//....//var/log/apache2/access.log&poison=ls , ta được:

![image](https://user-images.githubusercontent.com/104350480/232416732-2203b627-282d-4bf8-a699-63c7d8363de9.png)

Giờ cần tìm vị trí file flag và đọc nó ra là xong, thay ls thành ls / xem được gì: 

![image](https://user-images.githubusercontent.com/104350480/232417077-f7172281-f22a-4d10-aae7-8d69a380267d.png)

Ta có được file secret_fl4g.txt, tiếp tục cmd: &poison=cat /secret_fl4g.txt ta được flag của bài: 

![image](https://user-images.githubusercontent.com/104350480/232417516-17a28318-483f-4f59-8080-aeec743a5d31.png)

> Flag: jctf{4PachE_L0G_POiS0nInG}


## [7. xss-terminator](http://198.211.99.71:3000/)

![image](https://user-images.githubusercontent.com/104350480/232418058-74501bc5-8c33-477c-9606-faa9297c1237.png)

Giao diện của bài: 

![image](https://user-images.githubusercontent.com/104350480/232418129-943f91be-d620-4635-bf01-f2550f3a8c25.png)


![image](https://user-images.githubusercontent.com/104350480/232418176-ace4d091-0bff-49a7-8f45-8fa46d0a56b5.png)

Thử xss cơ bản xem sao: 

![image](https://user-images.githubusercontent.com/104350480/232418413-6794c250-787d-4efd-950c-2db50bd5897d.png)

Kh alert được, ta thử cái khác, <svg/onload=alert(document.domain)>

Nhưng bài này kh có chỗ nộp để lấy document.cookie, đọc lại đề bài chút:

![image](https://user-images.githubusercontent.com/104350480/232418579-8bfdb8b0-0c8c-4fd9-9f5e-89067f47681b.png)


```
The Terminator told you to put the cookie down, and you didn't listen. Now he is very angry! You must find a way to steal the cookie from the vulnerable website and send it to the evil server where the Terminator is waiting... just don't make him wait too long.

```

Ở đây bài nói cookie chưa được hạ xuống, có vẻ vẫn lưu trên web và dựa vào giao diện web t2 thì hướng đi giờ chỉ cần lấy document.cookie và thực hiện như này là õng: **Steal the cookie and send it to my /cookie?data endpooint** , tên là bài hard nhưng hơi lạ.

Ta chỉ việc vào thẳng web lấy: 

![image](https://user-images.githubusercontent.com/104350480/232419459-dfe4d57e-8723-4acc-872b-227439457966.png)

Và sau đó gửi nó qua bên trang web còn lại để lấy flag: 

![image](https://user-images.githubusercontent.com/104350480/232419696-19f73d14-714c-4e6f-9eb0-2e0fb47948f5.png)

Load lại trang là có flag:

![image](https://user-images.githubusercontent.com/104350480/232419832-6f432f4e-7333-4199-92f3-aacc4dc11847.png)

> Flag: Flag: jctf{who_said_you_could_open_the_cookie_jar!?}

## [8. avenge-my-password](http://159.203.191.48/)


![image](https://user-images.githubusercontent.com/104350480/232690800-6374c3a3-2567-420a-b7b9-965852b88593.png)

Ta truy cập đường dẫn và nhập thử: 

![image](https://user-images.githubusercontent.com/104350480/232692157-49ef18a5-3363-4683-a5db-9b6703ee0395.png)

Đề bài cho phép ta kết nối ssh sang máy chủ của họ: 

![image](https://user-images.githubusercontent.com/104350480/232692416-be1cf6fb-f059-4da2-9bc7-0a796d023b2b.png)

Check thử ls -lsa, có mysql_history, ta cat thử xem file có gì: 

![image](https://user-images.githubusercontent.com/104350480/232692554-dde91d94-4d2e-4571-b5a5-9409c47ed756.png)

Có vẻ là dữ liệu liên quan đến trang web đăng nhập ở trên, giờ ta truy cập thử vào mysql của hệ thống xem có gì: 



```

import requests
usernames = open("./username.txt","r").readlines()
usernames = [a.strip() for a in unsernames if a.strip != ""]
print(usernames)

for username in usernames:
    print(f"{username}=")
    r = requests.post("http://159.203.191.48/", data = {"username":username, "password":password, "submit":"Login"})
    if "Invalid" not in r.text:
        print(r.text)
        break

```










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
