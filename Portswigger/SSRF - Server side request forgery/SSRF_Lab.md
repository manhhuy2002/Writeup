# Tran Manh Huy 

* [Lab 1: Basic SSRF against the local server](#lab-1-basic-ssrf-against-the-local-server)
* [Lab 2: Basic SSRF against another backend system](#lab-2-basic-ssrf-against-another-backend-system)
* [Lab 3: SSRF with blacklist-based input filter](#lab-3-ssrf-with-blacklist-based-input-filter)

## Lab 1: Basic SSRF against the local server

```
 This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos. 

```

- Bài lab chứa lỗi ở phần check-stock, chỉ cần thay đổi đường dẫn đến phần data để lấy dữ liệu bằng ** http://localhost/admin ** như gợi ý là xong

![lab1_01](https://github.com/manhhuy2002/hello-world/blob/main/ssrf/lab1_01.jpg)

- Hiện luôn đường dẫn xóa user calos, thay bằng đường dẫn ** http://localhost/delete?username=carlos ** vào stokcApi , vậy là xóa thành công user này.

## Lab 2: Basic SSRF against another backend system
## Lab 3: SSRF with blacklist-based input filter
