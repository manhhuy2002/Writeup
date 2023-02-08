# Tran Manh Huy 

* [Lab 1: Basic SSRF against the local server](#lab-1-basic-ssrf-against-the-local-server)
* [Lab 2: Basic SSRF against another backend system](#lab-2-basic-ssrf-against-another-backend-system)
* [Lab 3: SSRF with blacklist-based input filter](#lab-3-ssrf-with-blacklist-based-input-filter)

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
