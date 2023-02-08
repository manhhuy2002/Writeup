# Tran Manh Huy - SSRF-Server side request forgery 

* [Lab 1: Basic SSRF against the local server](#lab-1-basic-ssrf-against-the-local-server)
* [Lab 2: Basic SSRF against another backend system](#lab-2-basic-ssrf-against-another-backend-system)
* [Lab 3: SSRF with blacklist-based input filter](#lab-3-ssrf-with-blacklist-based-input-filter)
* [Lab 4: SSRF with filter bypass via open redirection vulnerability](#lab-4-ssrf-with-filter-bypass-via-open-redirection-vulnerability)
* [Lab 5: Blind SSRF with out-of-band detection](#lab-5-blind-ssrf-with-out-of-band-detection)
* [Lab 6: SSRF with whitelist-based input filter](#lab-6-ssrf-with-whitelist-based-input-filter)
* [Lab 7: Blind SSRF with Shellshock exploitation](#lab-7-blind-ssrf-with-shellshock-exploitation)

To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server. 

## Lab 1: Basic SSRF against the local server

```
 This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos. 

```

- Bài lab chứa lỗi ở phần check-stock, chỉ cần thay đổi đường dẫn đến phần data để lấy dữ liệu bằng **http://localhost/admin** như gợi ý là xong

![lab1_01](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab1_01.jpg)

- Hiện luôn đường dẫn xóa user calos, thay bằng đường dẫn **http://localhost/delete?username=carlos** vào stokcApi , vậy là xóa thành công user này.

## Lab 2: Basic SSRF against another backend system

```
 This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal 192.168.0.X range for an admin interface on port 8080, then use it to delete the user carlos

```
- Vẫn tương tự như lab 1 nhưng đường dẫn đến trang giao diện của quản trị viên kh còn là localhost mà là **http:192.168.0.X:8080/admin** , điền cần ở đây là tìm X trên giải từ 1-255, để scan nhanh X thì mình sẽ ném vào burp intruder rồi đặt payload type là number từ 1-255 và bắt dầu tấn công:

![lab2_01](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab2_01.jpg)

Sau khi tấn công để ý payload X = 241, trả về status 200 nên mình tìm được X, đến đây **Ctr R** để chuyển về **Burp repeater** rồi thay bằng đường dẫn **http://192.168.0.241:8080/admin/delete?username=carlos** là giải quyết xong bài lab.

![lab2_02](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab2_02.jpg)

## Lab 3: SSRF with blacklist-based input filter

```
 This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.

The developer has deployed two weak anti-SSRF defenses that you will need to bypass. 

```

### Ở bài này đã có filter, để ý tên bài thì đây là dạng filter blacklist, mình test thử thì thấy bài này đang filter localhost và admin.
#### Đầu tiên là thử với localhost:

![lab3_01](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab3_01.jpg)

#### Tương tự thử với admin:

![lab3_02](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab3_02.jpg)

#### Mục tiêu của bài này là phải bypass được 2 giá trị này với đường dẫn nội dung tương tự là http://localhost/admin
Vì phải bypass cả 2 giá trị này nên mình tìm cách bypass từng giá trị trước, localhost hay có dạng 127.0.0.1 thì có thể dùng ssrf bypass list cho localhost như sau:

```
http://127.1/
http://0000::1:80/
http://[::]:80/
http://2130706433/
http://whitelisted@127.0.0.1
http://0x7f000001/
http://017700000001
http://0177.00.00.01

```

#### Ném sang burp repeater chạy với đường dẫn: **http://127.1/** và đã bypass được localhost thành công, đến đây thì cần tiếp tục bypass admin là xong, ý tưởng là encode 1 chữ cái trong admin, thử với chữ a: thay bằng đường dẫn: **http://127.1/%61dmin** , send request nhưng server trả về lỗi blacklist: 

![lab3_03](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab3_03.jpg)

- Có vẻ server đã tự decode nên ở đây mình encode thêm phát nữa xem sao, gửi **http://127.1/%25%36%31dmin** và server đã trả về kết quả:

![lab3_04](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab3_04.jpg)


#### Đến đây thay đường dẫn bằng **http://127.1/%25%36%31dmin/delete?username=carlos** là giải quyết được bài lab.



> Reference link: https://twitter.com/LooseSecurity/status/1331270289733324805

## Lab 4: SSRF with filter bypass via open redirection vulnerability

```
 This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://192.168.0.12:8080/admin and delete the user carlos.

The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first. 

```
#### Bài lab này là dạng bài Open-redirection leads to SSRF. Ứng dụng có URL được phép chứa lỗ hổng chuyển hướng mở. Với điều kiện API được sử dụng để thực hiện yêu cầu HTTP back-end hỗ trợ redirect, mình tạo URL thỏa mãn filter và chuyển hướng đến mục tiêu back-end mà mình mong muốn.

#### Cụ thể trong bài lab này có chứa 2 lỗi về param là stockApi và path, path thì cho phép open-redirect còn stockApi thì chứa lỗi ssrf dù đã được filter, mục tiêu vẫn phải đưa được đường dẫn **http://192.168.0.12:8080/admin** vào stockApi và vượt qua nó.

Vẫn như thường lệ, đầu tiên check stock mình được param stockApi.

Sau đó để ý trên trang thì có đường dẫn cho phép chuyển trang ***Return to list| Next product*** , nhấn Next product:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab4_02.jpg)

Để ý trên burpsuite, có được param path chuyển hướng trang:

![lab4_03](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab4_03.jpg)

Copy path **/product/nextProduct?currentProductId=10&path** rồi thay vào giá trị stockApi với đường dẫn ***path=http://192.168.0.12:8080/admin***. Gửi send request thì server trả về ***"Missing parameter 'path'"***. 

![lab4_04](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab4_04.jpg)

Quên mất mình cần chuyển hướng trang thôi mà, nên thay đổi đường dẫn như sau: 

> /product/nextProduct?path=http://192.168.0.12:8080/admin

Được kết quả trả về thành công: 

![lab4_05](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab4_05.jpg)

### Đến đây thay đường dẫn '**stockApi=/product/nextProduct?path=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos**' để xóa user Carlos là giải quyết xong bài lab.

> Người dùng có thể phải chịu các cuộc tấn công phishing bởi việc bị redirect sang các trang kh đáng tin cậy mà hacker tạo ra. Nếu người dùng chẳng may ấn vào đường link chuyển hướng có chứa mã độc được thực hiện trên 1 trang web đáng tin cậy thì hoàn toàn việc tấn công sẽ xảy ra. Sau đó, các Phisher có thể đánh cắp thông tin đăng nhập của người dùng và sử dụng các thông tin này để truy cập trang web hợp pháp.

## Lab 5: Blind SSRF with out-of-band detection

```
This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server. 

```

### Vấn đề của bài lab này là ở Referer header khi page product được load. Mục tiêu của bài lab này là khai thác ssrf thông qua phần **referer header** bằng việc tạo http request đến burp collaborator. Cũng khá đơn giản, chỉ việc dùng burp collaborator có sẵn trong burp suite rồi đổi đường dẫn link ở **referer header** bằng đường dẫn của burp collaborator. 
Cụ thể khi load page, mình được: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab5_01.jpg)

Thay đường dẫn: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab5_02.jpg)

Mình có thể check phản hồi ở bên burp collaborator, vậy là xong bài lab này: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab5_03.jpg)


> Bài này Portswigger bắt phải dùng Burp Collaborator. Thì burp collaborator là 1 tool của burpsuite cho phép ta nhận diện và khai thác lỗ hổng bên phía client của website/application. Nó hoạt động bằng việc chạy 1 server duy nhất có thể nhận được payload từ ứng dụng mục tiêu, cho phép người dùng xác định xem có bất kỳ thành phần phía client nào đang gửi thông tin nhạy cảm đến miền của bên thứ ba hay không. Máy chủ Collaborator lắng nghe các tương tác với payload và tạo report chi tiết các tương tác đã diễn ra.



## Lab 6: SSRF with whitelist-based input filter

```
 LAB Solved

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.

The developer has deployed an anti-SSRF defense you will need to bypass. 

```

#### Ở tình huống bài lab này, ta đến với phương án được triển khai trên web là ***whitelist-based input filter***. Thử nhập đường dẫn **http://localhost/admin** , server phản hồi về: ***"External stock check host must be stock.weliketoshop.net"*** . 
![]()

#### Qua quá trình tìm hiểu thì ở đây chúng ta có thể khai thác bằng URL parsing:
- Sử dụng kí tự “@” để đưa credential vào URL ngay phía trước hostname. Cụ thể ở đây là stock.weliketoshop.net. Ex: http://stock.weliketoshop.net@localhost :)
- Sử dụng kí tự “#” để phân đoạn cái URL theo kiểu https://evil-host#expected-host.
- Tận dụng DNS naming hierarchy để bố trí input yêu cầu của whitelist vào cái fully-qualified DNS name mà ta có thể kiểm soát theo kiểu https://expected-host.evil-host.
- Sử dụng URL-encode characters để chọc ngoáy chức năng URL-parsing của hệ thống nhằm khai thác sự sai khác trong quá trình parsing của chỗ filter với phần xử lý request của backend.
- Và kết hợp các phương pháp trên lại để bypass whitelist này.






