
* [Portswigger - Cross-site request fogery](#pcsrf)
  - [1. CSRF vulnerability with no defenses](#pcsrf1)
  - [2. CSRF where token validation depends on request method](#pcsrf2)
  - [3. CSRF where token validation depends on token being present](#pcsrf3)
  - [4. CSRF where token is not tied to user session](#pcsrf4)
  - [5. CSRF where token is tied to non-session cookie](#pcsrf5)
  - [6. CSRF where token is duplicated in cookie](#pcsrf6)
  - [7. SameSite Lax bypass via method override](#pcsrf7)
  - [8. SameSite Strict bypass via client-side redirect](#pcsrf8)
  - [9. SameSite Strict bypass via sibling domain](#pcsrf9)
  - [10. SameSite Lax bypass via cookie refresh](#pcsrf10)
  - [11. CSRF where Referer validation depends on header being present](#pcsrf11)
  - [12. CSRF with broken Referer validation]()

## What is Csrf?
- csrf hay cross-site request fogery là một lỗ hổng bảo mật web cho phép kẻ tấn công khiến người dùng thực hiện các hành động mà họ không có ý định thực hiện. Nó cũng như xss cho phép kẻ tấn công phá vỡ sop (same origin policy), được thiết kế để ngăn chặn các trang web khác nhau can thiệp lẫn nhau. 
Khi thực hiện một cuộc tấn công csrf kẻ tấn công sẽ tạo ra một trang web giả mạo, trong đó chứa các yêu cầu http được thiết kế để khai thác lỗ hổng của trình duyệt web người dùng. Khi người dùng truy cập vào trang web giả mạo, các yêu cầu http sẽ được tự động thực hiện đến 1 trang web khác mà người dùng đã đăng nhập trước đó. Do đó kẻ tấn công có thể lừa được hệ thống để thực hiện các hành động không mong muốn mà không cần biết được tên người dùng và mật khẩu của họ.

Một ví dụ về tấn công csrf có thể là khi kẻ tấn công tạo một trang web giả mạo đăng nhập vào trang web của ngân hàng. Khi người dùng truy cập vào trang web giải mạo và thực hiện các hành động trên trang đó, các yêu cầu http sẽ được gửi đến trang web ngân hàng mà không được biết đến. Kẻ tấn công có thể sử dụng các yêu cầu này để thực hiện các hành động không mong muốn chẳng hạn như chuyển tiền hoặc thay đổi thông tin tài khoản của người dùng. (các yêu cầu HTTP được tự động gửi đến trang web ngân hàng mà không cần sự cho phép hay sự thực thi của người dùng. Điều này là do trình duyệt web của người dùng đã lưu trữ các cookie đăng nhập của trang web ngân hàng, và các cookie này được gửi đến trang web ngân hàng khi các yêu cầu HTTP được thực hiện).

Ở đây ta sẽ thấy cách thức của csrf có phần giống với xss. Tuy nhiên thì chúng có sự khác nhau về cách thức tấn công và mục đích tấn công.

- XSS: Tấn công xss thường nhắm vào việc chèn mã độc js hoặc html vào các trang web mà người dùng có thể truy cập, để lừa đảo hoặc đánh cắp thông tin người dùng. Điều này xảy ra khi trang web không xác thực trang đầu vào của trang web mà người dùng có thể truy cập, hoặc không mã hóa đầu vào đúng cách.
- CSRF: tấn công csrf thường nhắm vào việc khai thác việc người dùng đã đăng nhập vào một trang web nào đó. Khi kẻ tấn công tạo ra các yêu cầu http giả mạo trên trang web giả mạo của họ, các yêu cầu sẽ được gửi đến trang web mà người dùng đã đăng nhập trước đó, để thực hiện được hành động mà người dùng không mong muốn trên trang. Điều này có thể xảy ra khi các trang web khong yêu cầu xác thực nguồn gốc của http hoặc khong sử dụng các biện pháp bảo mật để ngăn chặn tấn công csrf. Tấn công xss có thể được phòng ngừa bằng cách kiểm tra và xử lí đầu vào của người dùng trước khi hiển thị lên trang web.

Tóm lại sự khác nhau cơ bản nhất giữa xss và csrf là mục đích tấn công. Tấn công xss nhằm vào việc đánh cắp thông tin người dùng hoặc thực hiện các hành động dodjc hại trên trang web, trong khi đó thì tấn công csrf nhắm vào việc khai thác sự đăng nhập của người dùng để thực hiện các hành động không mong muốn trên trang web mà người dùng đã đăng nhập tước đó. Tấn công csrf có thể được phòng ngừa bằng cách sử dụng các biện pháp bảo vệ như mã thông báo csrf và cơ thế xác thực kép (double-submit cookies), kiểm tra tham chiếu (referer header) trong yêu cầu http, và giám sát các hoạt động đáng ngờ trên trang web của họ. 

Hậu quả của xss thường là nghiêm trọng hơn so với lỗ hổng csrf: 
- csrf thường chỉ áp dụng cho một số hành động mà người dùng có thể thực hiện, nhiều ứng dụng triển khai hệ thống phòng thủ csrf nói chung nhưng bỏ sót một hoặc hai hành động bị lộ ra. Ngược lại một cuộc tấn công xss mà thành công thì thường có thể giúp kẻ tấn công thực hiện bất kỳ hành động nào mà người dùng có thể thực hiện.
- Csrf có thể được mô tả là lỗ hổng 1 chiều, trong đó kẻ tấn công có thể thuyết phục nạn nhân thực hiện một số http, nhưng không thể lấy lại phản hồi từ yêu cầu đó ( tức là ta có thể hiểu ở đây là khi mà kẻ tấn công gửi yêu cầu thực hiện cho nạn nhân ). Ngược lại, xss là 'hai chiều', bởi vì mã độc được chèn của kẻ tấn công có thể đưa ra các yêu cầu tùy ý, đọc các phản hồi và đánh cắp dữ liệu đến một trang khác do kẻ tấn công chọn.


## Phòng thủ chống lại csrf: 

Ngày nay việc tìm kiếm và khai thác thành công các lỗ hổng csrf thường liên quan đến việc bỏ qua các biện pháp chống csrf được triển khai bởi trang web mục tiêu, trình duyệt của nạn nhân hoặc cả hai. Các biện pháp bảo vệ phổ biến nhất mà ta sẽ gặp phải như:
- Csrf token - csrf token là một giá trị duy nhất, bí mật và không thể đoán trước được tạo ra bởi ứng dụng phía server và được chia sẻ với client. Khi cố gắng thực hiện các hành động nhạy cảm, chẳng hạn như gửi biểu mẫu, client phải bao gồm mã csrf token chính xác trong yêu cầu. Điều này giúp cho việc tấn công bên hacker trở nên khó khăn trong việc tạo ra 1 request hợp lệ.

- SameSite cookies: Samesite là một cơ chế bảo mật của trình duyệt xác định khi nào cookie của 1 trang web được bao gồm trong các request bắt nguồn từ các trang web khác. Vì yêu cầu thực hiện các hành động nhạy cảm thường yêu cầu cookie session được xác thực, các hạn chế SameSite thích hợp để ngăn chặn kẻ tấn công kích hoạt các hành động này trên trang web.

Có 2 loại samesite là lax và strict:
+ Strict: đây là tùy chọn trong đó same-site được áp dụng nghiêm ngặt. Khi các thuộc tính samesite được đặt là strict, cookie sẽ không được gửi cùng các request được bắt đầu bởi các trang web của bên thứ 3. Đặt cookie là strict có thể ảnh hưởng tiêu cự đến trải nghiệm trình duyệt web. Ví dụ nếu ta nhấp vào một liên kết đến trang profile của Facebook và Facebook.com đặt cookie của nó là samesite=strict thì ta sẽ kh thể tiếp tục redirect trên facebook trừ khi ta đăng nhập vào facebook. Lý do là vì cookie của facebook không được gửi kèm với request này.

+ Lax: khi ta đặt thuộc tính samesite của cookie thành lax, cookie sẽ được gửi cùng với get request được tạo bởi bên thứ 3, ngoài ra còn có các phương thức request an toàn khác như head, options, trace. Còn phương thức như POST thường là kiểu http 'không an toàn' nên cookie không được thực thi khi samesite=lax.

Xác thực dựa trên referer: một số ứng dụng sử dụng tiêu đề referer http để cố gắng chống lại các cuộc tấn công csrf, thông thường bằng cách xác minh rằng yêu cầu bắt nguồn từ miền riêng cửa ứng dụng. Điều này thường kém hiệu quả hơn csrf token nêu ở trên.

Ta sẽ đi sâu vấn đề hơn và làm bài lab của từng phần:


## Bypassing CSRF token validation

### [1. CSRF where token validation depends on request method](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-request-method)

Bài lab này mô phỏng việc website xác thực csrf token phục thuộc vào phương thức yêu cầu: 
Đầu tiên ta vào phần đăng nhập với mục đích bắt request của thay đổi email:

![image](https://user-images.githubusercontent.com/104350480/224540812-bd860831-cc62-4f9c-a601-396ab6872366.png)

Ở phương thức POST có csrf token đi kèm:

![image](https://user-images.githubusercontent.com/104350480/224540814-63ecccda-fe77-4ff2-877a-8a1f2dc0c11e.png)

Bỏ đi thì sẽ báo lỗi là rõ:

![image](https://user-images.githubusercontent.com/104350480/224540815-5ce6caf3-f646-4952-8451-7710a96485be.png)

Mà bài này thì chuyển về phương thức GET lại không: 

![image](https://user-images.githubusercontent.com/104350480/224540823-03d251b0-c4b5-4c5e-ac72-d13dbb1f39bd.png)

Viết form đơn giản gửi qua phần exploit: 

![image](https://user-images.githubusercontent.com/104350480/224540834-1e3e83b1-b324-42a2-b82e-2310600e2213.png)

```
<form id='csrf' action='https://0ac90074044080ffc16121c5000c0026.web-security-academy.net/my-account/change-email' method='GET'>
<input type='hidden' name='email' value='manhhuy08112002@gmailcom'>
<input type='submit'
</form/>
<script>
document.getElementById('csrf').submit();

</script>

```

### [2. ]()
