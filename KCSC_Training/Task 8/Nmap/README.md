# Nmap

* [Introduction](#nm1)
* [Nmap Switches](#nm2)
* [Scan Types Overview](#nm3)
* [Scan Types TCP Connect Scans](#nm4)
* [Scan Types SYN Scans](#nm5)
* [Scan Types UDP Scans](#nm6)
* [Scan Types NULL, FIN and Xmas](#nm7)
* [Scan Types ICMP Network Scanning](#nm8)
* [NSE Scripts Overview](#nm9)
* [NSE Scripts Working with the NSE](#nm10)
* [NSE Scripts Searching for Scripts](#nm11)
* [Firewall Evasion](#nm12)
* [Practical](#nm13)


<hr>


## Introduction

Giả sử ta được cung cấp một địa chỉ IP (hoặc nhiều địa chỉ IP) để thực hiện một cuộc đánh giá bảo mật. Trước khi làm bất cứ điều gì khác, ta cần có một ý tưởng về mục tiêu mà ta đang tấn công. Điều này có nghĩa là ta cần xác định các dịch vụ đang chạy trên các mục tiêu.
Ví dụ, có thể một trong số chúng đang chạy một máy chủ web, và một cái khác đang hoạt động như một điều khiển miền Windows Active Directory. Bước đầu tiên để xác định là quét cổng. Khi 1 máy tính chạy một dịch vụ mạng, nó mở một kết nối mạng gọi là "port" để nhận kết nối. Các cổng là cần thiết để thực hiện nhiều yêu cầu mạng hoặc có nhiều dịch vụ khả dụng. Ví dụ, khi ta tải nhiều trang web cùng một lúc trong trình duyệt web, chương trình phải có một cách nào đó để xác định tab nào đang tải trang web nào. Điều này được thực hiện bằng cách thiết lập kết nối đến các máy chủ web từ xa bằng các cổng khác nhau trên máy tính của ta. Tương tự, nếu ta muốn một máy chủ có thể chạy nhiều hơn một dịch vụ (ví dụ, có thể ta muốn máy chủ web của mình chạy cả phiên bản HTTP và HTTPS của trang web), thì ta cần một cách nào đó để điều hướng lưu lượng đến dịch vụ thích hợp. Một lần nữa, các cổng là giải pháp cho điều này. Kết nối mạng được thực hiện giữa hai cổng - một cổng mở nghe trên máy chủ và một cổng được chọn ngẫu nhiên trên máy tính của ta. Ví dụ, khi ta kết nối đến một trang web, máy tính của ta có thể mở cổng 49534 để kết nối đến cổng 443 của máy chủ.Giả sử ta được cung cấp một địa chỉ IP (hoặc nhiều địa chỉ IP) để thực hiện một cuộc đánh giá bảo mật. Trước khi làm bất cứ điều gì khác, ta cần có một ý tưởng về "bản đồ" ta đang tấn công. Điều này có nghĩa là ta cần xác định các dịch vụ đang chạy trên các mục tiêu. Ví dụ, có thể một trong số chúng đang chạy một máy chủ web, và một cái khác đang hoạt động như một điều khiển miền Windows Active Directory. Các bước đầu tiên để xác định "bản đồ" của tình hình này là quét cổng. Khi một máy tính chạy một dịch vụ mạng, nó mở một kết nối mạng gọi là port để nhận kết nối. Các cổng là cần thiết để thực hiện nhiều yêu cầu mạng hoặc có nhiều dịch vụ khả dụng. Ví dụ, khi ta tải nhiều trang web cùng một lúc trong trình duyệt web, chương trình phải có một cách nào đó để xác định tab nào đang tải trang web nào. Điều này được thực hiện bằng cách thiết lập kết nối đến các máy chủ web từ xa bằng các cổng khác nhau trên máy tính của ta. Tương tự, nếu ta muốn một máy chủ có thể chạy nhiều hơn một dịch vụ (ví dụ, có thể ta muốn máy chủ web của mình chạy cả phiên bản HTTP và HTTPS của trang web), thì ta cần một cách nào đó để điều hướng lưu lượng đến dịch vụ thích hợp. Một lần nữa, các cổng là giải pháp cho điều này. Kết nối mạng được thực hiện giữa hai cổng - một cổng mở lắng nghe trên máy chủ và một cổng được chọn ngẫu nhiên trên máy tính của ta. Ví dụ, khi ta kết nối đến một trang web, máy tính của ta có thể mở cổng 49534 để kết nối đến cổng 443 của máy chủ.

![image](https://user-images.githubusercontent.com/104350480/237042963-21fb1b46-fa3d-4f70-aeef-664774f8a398.png)
