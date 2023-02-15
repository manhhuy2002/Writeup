# Tìm hiểu về JWT
<hr>

> Reference: https://datatracker.ietf.org/doc/html/draft-ietf-oauth-json-web-token-32#section-4.1

## Trước hết đi vào jwt, ta cần hiểu jwt là gì? Và một vài đặc điểm của jwt, cách thức tấn công cũng như là sự khác biệt giữa jwt với cookie và session?

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
  + Public Claim Names: Đây là các claim tùy ý được do người dùng tự định nghĩa theo ý muốn sử dụng jwt, và có thể sử dụng để định nghĩa người dùng hoặc ứng 
  dụng dùng jwt. 
  + Private Claim Names: Đây là claim tùy ý được định nghĩa bởi tổ chức sử dụng jwt và kh được chia sẻ với bất cứ ai khác, các private claim có thể được sử dụng để định nghĩa các thông tin xác thực cụ thể về người dùng.
  - Ví dụ về payload với người dùng có quyền admin:
  ![image](https://user-images.githubusercontent.com/104350480/219039888-b987c874-6f59-428a-b918-1da543fa78f9.png)
  
#### Cuối cùng là Signature, signature là 1 chuỗi kí tự với sự kết hợp giữa 2 thành phần là header và payload được phân cách với nhau bởi dấu chấm, secret key và một thuật toán mã hóa được chỉ định dùng để mã hóa.  Ví dụ về signature để dễ hiểu hơn:
  
  + Header: 
  
  ![image](https://user-images.githubusercontent.com/104350480/219042363-4e9fccc9-b130-4d74-8953-0308cd3901e7.png)

  + Payload: 
  
  ![image](https://user-images.githubusercontent.com/104350480/219042511-2edfdf27-eb98-4415-8cb1-ff565b452c14.png)
  
  Ở đây chúng ta sẽ dùng thuật toán HS256 để tạo signature. Thuật toán này sử dụng 1 secretkey để tạo signature, giả sử secretkey của chúng ta là 
  **kcscsecretkey**, để tạo signature ta thực hiện như sau: 
  
