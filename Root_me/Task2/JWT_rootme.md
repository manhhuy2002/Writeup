# Tìm hiểu về JWT
<hr>

> Reference: https://datatracker.ietf.org/doc/html/draft-ietf-oauth-json-web-token-32#section-4.1

## 1. Trước hết đi vào jwt, ta cần hiểu jwt là gì? Và một vài đặc điểm của jwt, cách thức tấn công cũng như là sự khác biệt giữa jwt với cookie và session?

- JWT được viết tắt của json web token, là 1 tiêu chuẩn mở được mã hóa dùng để truyền thông tin qua môi trường web một cách an toàn, và nhanh chóng hơn.
Hiện nay thì jwt được sử dụng 1 cách rộng rãi trên các trang web vì nó đơn giản và linh hoạt, ngoài ra cũng rất an toàn nếu thực hiện đúng cách. 

- JWT gồm 3 phần chính là: header, payload và signature, được nối và phân cách với nhau bởi dấu chấm. Cấu trúc của nó đơn giản như sau:

```
 <base64-encoded header>.<base64-encoded payload>.<HMACSHA256(base64-encoded signature)>    
```
#### Trong đó thì header thường bao gồm 2 thành phần chính là typ (type) và alg (alogrithm), tức chứa thông tin về loại token, và thuật toán để mã hóa token:
  ++ type: thông thường là'JWT' 
  ++ algorithm: thuật toán được mã hóa được sử dụng để tạo singnature đi kèm với token.
#### Payload thì chứa thông tin xác thực cụ thể của người dùng hoặc ứng dụng. Trong đó nó là json obj chứa các attribute tùy chọn. Các attribute này được gọi là claims. Nhìn chung ta có thể phân ra làm 3 loại: Registered Claim Names, Public Claim Names, và Private Claim Names:
  ++ Registrered Claim Names: Đây là thuộc tính được định sẵn  trong IANA JSON Web Token Claims registry. Những thuộc tính này là kh bắt buộc nhưng ta có thể
  ứng dụng nó tùy theo tình huống:
   - iss (issuer): chỉ ra định danh của người hoặc nhà cung cấp jwt
   - sub (subject): Chỉ ra đối tượng mà JWT đang xác thực, thường là ID người dùng.
   - aud (audience) : chỉ ra đích danh của người hoặc ứng dụng nhận jwt.
   - exp (expiration time) : thời điểm mà token kh còn hiệu lực.
   - nbf (not before) : chỉ ra thời điểm trước đó mà kh thể sử dụng jwt.
   - iat (issued at) : thời điểm token được phát hành và được tính theo UNIX TIME
   - jti (jwt id) : ID của JWT
 ++ Public Claim Names: Đây là các claim tùy ý được do người dùng tự định nghĩa theo ý muốn sử dụng jwt, và có thể sử dụng để định nghĩa người dùng hoặc ứng 
  dụng dùng jwt. 
 ++ Private Claim Names: Đây là claim tùy ý được định nghĩa bởi tổ chức sử dụng jwt và kh được chia sẻ với bất cứ ai khác, các private claim có thể được sử dụng để định nghĩa các thông tin xác thực cụ thể về người dùng.
  - Ví dụ về payload với người dùng có quyền admin:
  ![image](https://user-images.githubusercontent.com/104350480/219039888-b987c874-6f59-428a-b918-1da543fa78f9.png)
  
#### Cuối cùng là Signature, signature là 1 chuỗi kí tự với sự kết hợp giữa 2 thành phần là header và payload được phân cách với nhau bởi dấu chấm, secret key và một thuật toán mã hóa được chỉ định dùng để mã hóa.  Ví dụ về signature để dễ hiểu hơn:
  
  ++ Header: 
  
  
  ![image](https://user-images.githubusercontent.com/104350480/219042363-4e9fccc9-b130-4d74-8953-0308cd3901e7.png)
 
 ```
 {"alg": "HS256", "typ": "JWT"}

```


  ++ Payload: 

  ```
    {"sub": "1234567890", "name": "Manh Huy", "iat": 1516239022}
    
  ```
  
  ![image](https://user-images.githubusercontent.com/104350480/219042511-2edfdf27-eb98-4415-8cb1-ff565b452c14.png)
  
  Ở đây chúng ta sẽ dùng thuật toán HS256 để tạo signature. Thuật toán này sử dụng 1 secretkey để tạo signature, giả sử secretkey của chúng ta là 
  **kcscsecretkey**, để tạo signature ta thực hiện như sau: 
  - Tạo 1 chuỗi json bao gồm header và payload nối nhau bởi dấu chấm: 
    > eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiAiMTIzNDU2Nzg5MCIsICJuYW1lIjogIk1hbmggSHV5IiwgImlhdCI6IDE1MTYyMzkwMjJ9

  - Sau đó sử dụng secret key và thuật toán HS256 để tạo signature từ chuỗi JSON này:
  > HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  mysecretkey )
  
  #### Ở đây ta dùng script python để tạo chuỗi này :
  
  ```
  
  import hmac
import hashlib
import base64
import json

header = {"alg":"HS256", "typ":"JWT"}
payload = {"sub": "1234567890", "name": "Manh Huy", "iat": 1516239022}
# dùng base64.urlsafe_b64encode ở đây để mã hóa hoặc decode, dùng urlsafe để thay thế các ký tự đặc biệt như '+','/','='
# thành '-','_' nếu có và loại bỏ các ký tự padding = cuối cùng khỏi chuỗi mã hóa, vì các ký tự kia khi truyền qua
# internet có thể gây rắc rối. Giúp chuỗi base64url được truyền và xử lí dễ dàng hơn.
header_encoded = base64.urlsafe_b64encode(json.dumps(header).encode()).decode('utf-8')
payload_encoded = base64.urlsafe_b64encode(json.dumps(payload).encode()).decode('utf-8')
data = header_encoded +'.'+ payload_encoded
# tạo secret key để sign data
secret = 'kcscsecretkey'.encode()
# sign data  với thuật toán hmac-sha256 kết hợp với secretkey
signature = hmac.new(secret, data.encode(), hashlib.sha256).digest()
# ta encode signature dưới dạng base64
signature_encoded = base64.urlsafe_b64encode(signature).decode('utf-8').rstrip('=')
# Nối chuỗi header, payload và signature là ta tạo thành công 1 jwt
jwt = data +'.'+signature_encoded
print(jwt)

```

Như vậy kết hợp lại 3 phần ta có 1 chuỗi jwt như sau, vậy là hiểu được thành phần và quá trình tạo nên 1 jwt:
> eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiAiMTIzNDU2Nzg5MCIsICJuYW1lIjogIk1hbmggSHV5IiwgImlhdCI6IDE1MTYyMzkwMjJ9.qh5sCnBphQzw4Bgb2H-LMBioumbuSEpWRV2c22evI_c

#### Quên mất ta chưa trả lời câu hỏi jwt này thì có gì khác với cookie và session, triển tiếp thôi: 

Thì về cơ bản ta đã hiểu được là jwt là một tiêu chuẩn mã hóa thông tin trong môi trường web, giúp xác thực dữ liệu đồng thời giải quyết được một số vấn
đề mà cookie và session vướng phải:
- Thứ nhất: jwt là một phương thức xác thực stateless, tức là jwt ở trạng thái kh cần lưu trên máy chủ. Trong khi đó cookie và session thì có, nó cần phải 
được lưu lại trên phía máy khách/máy chủ, nếu số lượng ít thì kh sao nhưng nếu số lượng lớn thì lại khác, sẽ làm chậm quá tốc độ xử lí của phía máy chủ, đây là điểm 
cộng đầu tiên của jwt.
- Thứ hai: jwt cho phép ta trao đổi thông tin xác thực giữa các tên miền khác nhau bằng cách sử dụng 1 token chứa thông tin xác thực, còn cookie và session thì không, chúng chỉ có thể hoạt động được trên cùng 1 tên miền
- Thứ ba: Khi một ứng dụng cookie để lưu trữ thông tin xác thực, người dùng có thể dễ dàng chỉnh sửa cookie trên trình duyệt, với jwt thì khác, nó sử dụng 
thuật toán được mã hóa ở trên và chỉ sử dụng cùng 1 thuật toán mới giải mã được, nên jwt có khả năng bảo mật sẽ tốt hơn so với cookie....
Nói chung ta có thể thấy sơ qua được 1 vài ưu điểm của jwt như vậy. 

## 2.Ta đến với phần 2, tìm hiểu tất tần tật về jwt attack:  

### 2.1 Tấn công jwt xảy ra khi nào?

 Tấn công jwt xảy ra khi người dùng sử dụng các jwt đã sửa đổi đến phía máy chủ mục tiêu có lỗi xử lí jwt trong việc xử lí. Có một vài cách để tấn công như: 
   - Tấn công XSS (Cross-site scripting): Tấn công XSS được thực hiện bằng cách chèn mã độc vào trang web và lấy cookie chứa JWT của người dùng.
   - Tấn công CSRF (Cross-site request forgery): Tấn công CSRF được thực hiện bằng cách khai thác việc sử dụng cookie trong JWT để thực hiện các yêu cầu giả
mạo từ một trang web khác. 
   - Tấn công bằng cách sử dụng thuật toán mã hóa yếu: Một số thuật toán mã hóa yếu có thể bị dò tìm hoặc tấn công bởi các kẻ tấn công. Ví dụ, thuật toán
HS256 đã bị chứng minh là có thể bị tấn công bằng cách dò tìm khóa bí mật.


#### 2.2 Tác động của tấn công jwt

Tác động của tấn công jwt là rất nghiêm trọng, nếu kẻ tấn công có thể lấy được jwt của một người dùng, chúng có thể mạo danh tài khoản của họ và truy cập vào
tài khoản của họ.

## 3. Lab portswigger

#### 3.1 : Lab: JWT authentication bypass via unverified signature

```
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

```

![image](https://user-images.githubusercontent.com/104350480/219689675-674cd62e-97be-430d-991f-0400da89f141.png)

Bài này khá đơn giản, lỗi có thể do dev nhầm lẫn giữa decode() và verify() nên chữ kí kh hề được xác minh, chỉ cần thay đổi sub trong payload thành administrator là  được.

![image](https://user-images.githubusercontent.com/104350480/219690319-84559823-6ce0-4ab5-b87d-bcd687934f0b.png)

Có đường dẫn rồi, sửa request thành **GET /my-account** thành **GET /admin/delete?username=carlos** là xong

![image](https://user-images.githubusercontent.com/104350480/219690588-5578a725-ea07-4544-978b-feb8f4d469da.png)

#### 3.2 : Lab: Lab: JWT authentication bypass via flawed signature verification

```
 This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter 

```
Đọc tên đề có thể thấy bài lab này là dạng bỏ qua xác thực jwt thông qua lỗi thiếu sót chữ kí, dạng này làm cho vui chứ thực tế thì hiếm là rõ rồi, vậy đổi alg thành none và sub thành administrator thôi, nhưng sau khi sửa thì thấy server trả về lỗi: 

![image](https://user-images.githubusercontent.com/104350480/219693913-e0c7c3d1-386d-41cd-9779-6c141fbb59cc.png)

Ngồi ngẫm 1 tý thì quên mất mình để alg = none rồi thì đâu cần signature nữa, xóa bỏ signature là được. Tương tự như trên thôi:

![image](https://user-images.githubusercontent.com/104350480/219694305-ed9d2e27-fee9-4de4-922d-7b6bf8c88f92.png)

> GET /admin/delete?username=carlos

#### 3.3: Lab: JWT authentication bypass via weak signing key

```
This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist of common secrets.

To solve the lab, first brute-force the website's secret key. Once you've obtained this, use it to sign a modified session token that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

```
Bài này thì là dạng weak signing key, nghe đã thấy là liên quan đến brute force để kiếm secret key rồi, triển thôi.


