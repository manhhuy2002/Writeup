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

## Practice

* [Easy Peasy](#ep)
* [Brooklyn Nine Nine](#bnn)
* [Net Sec Challenge](#nsc)
* [Anonymous](#a)


<hr>


## Introduction<a name="nm1"></a>

Giả sử ta được cung cấp một địa chỉ IP (hoặc nhiều địa chỉ IP) để thực hiện một cuộc đánh giá bảo mật. Trước khi làm bất cứ điều gì khác, ta cần có một ý tưởng về mục tiêu mà ta đang tấn công. Điều này có nghĩa là ta cần xác định các dịch vụ đang chạy trên các mục tiêu.

Bước đầu tiên để xác định là quét cổng. Khi 1 máy tính chạy một dịch vụ mạng, nó mở một kết nối mạng gọi là "port" để nhận kết nối. Các cổng là cần thiết để thực hiện nhiều yêu cầu mạng hoặc có nhiều dịch vụ khả dụng. Ví dụ, khi ta tải nhiều trang web cùng một lúc trong trình duyệt web, chương trình phải có một cách nào đó để xác định tab nào đang tải trang web nào. Điều này được thực hiện bằng cách thiết lập kết nối đến các máy chủ web từ xa bằng các cổng khác nhau trên máy tính của ta. Tương tự, nếu ta muốn một máy chủ có thể chạy nhiều hơn một dịch vụ (ví dụ, có thể ta muốn máy chủ web của mình chạy cả phiên bản HTTP và HTTPS của trang web), thì ta cần một cách nào đó để điều hướng lưu lượng đến dịch vụ thích hợp và ở đây cổng chính là giải pháp cho điều này. Kết nối mạng được thực hiện giữa hai cổng - một cổng mở nghe trên máy chủ và một cổng được chọn ngẫu nhiên trên máy tính của ta. Ví dụ, khi ta kết nối đến một trang web, máy tính của ta có thể mở cổng 49534 để kết nối đến cổng 443 của máy chủ.



![image](https://user-images.githubusercontent.com/104350480/237042963-21fb1b46-fa3d-4f70-aeef-664774f8a398.png)

Mỗi máy tính có tổng cộng 65535 cổng có sẵn, tuy vậy thì nhiều trong số này được đăng ký làm cổng tiêu chuẩn. Ví dụ: dịch vụ web HTTP gần như luôn có thể được tìm thấy trên cổng 80 của máy chủ. Một dịch vụ web HTTPS có thể được tìm thấy trên cổng 443.

Và ở đây nếu ta kh biết những cổng nào đang mở thì khó có thể tấn công được mục tiêu, cũng như được sử dụng để tăng cường khả năng tấn công hay cho mục đảm bảo an ninh, do đó nmap được sử dụng. Nmap là một công cụ phổ biến để thực hiện quá trình quét cổng và có nhiều tính năng mạnh mẽ, bao gồm khả năng phát hiện lỗ hổng và thực hiện các cuộc tấn công. Nmap là công cụ được sử dụng nhiều trong ngành và là lựa chọn hàng đầu cho việc liệt kê ban đầu trên một mục tiêu. Ta sẽ đi sâu hơn vào phần sau. 

## Nmap Switches<a name="nm2"></a>

Ở phần này ta sẽ viết lại 1 số các option thường được sử dụng cũng như tên đầy đủ của chúng để hiểu và nhớ rõ hơn.

>  What is the first switch listed in the help menu for a 'Syn Scan' (more on this later!)? : 

![image](https://user-images.githubusercontent.com/104350480/237048068-0defad99-56fe-410f-9393-3a45a72298ac.png)

-sS ở đây được gọi là "TCP SYN SCAN" . Nếu cổng đang mở, hệ thống sẽ gửi lại một gói tin SYN-ACK với cờ ACK được đặt trên. Nếu cổng đó không phản hồi hoặc phản hồi với gói tin khác, ta có thể xác định rằng cổng đó không mở.

Tương tự thì với câu hỏi: Which switch would you use for a "UDP scan"?: -sU 

> If you wanted to detect which operating system the target is running on, which switch would you use?

Để phát hiện hệ điều hành đang chạy thì ta dùng : -O


Cung cấp thông tin về version đang chạy: -sV
> Nmap provides a switch to detect the version of the services running on the target. What is this switch?  : 


Cung cấp chi tiết thông tin đầu ra: -V (hiệu quả hơn thì nên dùng -vv)
> The default output provided by nmap often does not provide enough information for a pentester. How would you increase the verbosity?


Để lưu đầu ra của NMAP ở ba định dạng chính ta có thể dùng: -oA (lowercase "o" and uppercase "A") 
> What switch would you use to save the nmap results in three major formats?

3 định dạng trong nmap là: 

```
Định dạng thông normal: Định dạng đầu ra dễ đọc cho con người, bao gồm thông tin về các cổng mở, dịch vụ đang chạy, trạng thái của các cổng và một số thông tin khác.

Định dạng XML: Định dạng đầu ra dưới dạng tệp XML, phù hợp để xử lý dữ liệu bằng các công cụ phân tích XML hoặc các chương trình khác.

Định dạng grepable: Định dạng đầu ra dưới dạng văn bản dễ tìm kiếm, phù hợp để xử lý dữ liệu bằng các công cụ tìm kiếm hoặc các lệnh grep trên Linux.

```


Muốn nmap quét 1 cổng cụ thể chẳng hạn như 80: -p 80, còn trong 1 khoảng cụ thể ví dụ từ 1000 đến 1500 thì dùng: -p 1000-1500
> How would you tell nmap to only scan port 80?

Để quét tất cả các cổng: -p-
> How would you tell nmap to scan all ports? 

> How would you activate a script from the nmap scripting library (lots more on this later!)? --script

> How would you activate all of the scripts in the "vuln" category? --script=vul


## Scan Types Overview

Khi quét cổng với nmap, sẽ có 3 loại scan cơ bản:
- TCP Connect Scans (-sT)
- SYN "Half-open" Scans (-sS)
- UDP Scans (-sU)

Ngoài ra sẽ còn vài loại scan port ít phổ biến nữa là: 
- TCP Null Scans (-sN)
- TCP FIN Scans (-sF)
- TCP Xmas Scans (-sX)

Ngoài ra còn có quét ICMP được sử dụng để kiểm tra tính khả dụng của một máy chủ hoặc mạng. Kỹ thuật này hoạt động bằng cách gửi các yêu cầu ping tới một máy chủ hoặc mạng và kiểm tra phản hồi. Nếu máy chủ hoặc mạng phản hồi, nó sẽ được xem là "khả dụng". Tuy nhiên, quét ICMP không phải là một phương pháp kiểm tra an ninh mạng toàn diện và có thể bị vô hiệu hóa hoặc chặn bởi các tường lửa hoặc bộ lọc mạng.

Chi tiết hơn ta sẽ bắt đầu đi vào cụ thể từng loại quét cổng. 

## Scan Types TCP Connect Scans<a name="nm5"></a>

Tóm tắt lại, ba giai đoạn của bắt tay ba bước (three-way handshake) bao gồm:

Bước 1: Máy khách (máy tấn công của chúng ta trong trường hợp này) gửi một yêu cầu TCP đến máy chủ mục tiêu với cờ SYN được đặt.
Bước 2: Máy chủ mục tiêu trả lời yêu cầu này với một gói tin TCP chứa cờ SYN và cờ ACK.
Bước 3: Máy khách hoàn tất bắt tay bằng cách gửi một yêu cầu TCP với cờ ACK được đặt.
Ba giai đoạn này tạo thành quá trình bắt tay ba bước trong TCP để thiết lập một kết nối mạng ổn định giữa máy khách và máy chủ. Quá trình bắt tay ba bước là một phần quan trọng của giao thức TCP và được sử dụng để thiết lập các kết nối mạng đáng tin cây:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/66ac877c-7a10-45de-9b04-feeb52500546)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/7db405f7-e30f-4529-afd5-a5d6cf031298)

TCP Connect hoạt động bằng cách thực hiện bắt tay ba bước với mỗi cổng đích một cách tuần tự. Ở đây Nmap cố gắng kết nối đến mỗi cổng TCP được chỉ định và xác định dịch vụ đang chạy trên cổng đó thông qua phản hồi mà nó nhận được.

RFC 793 là tài liệu quan trọng định nghĩa giao thức TCP, và nó quy định rằng:

```
Nếu kết nối không tồn tại (trạng thái CLOSED) thì một gói tin reset sẽ được gửi để phản hồi lại bất kỳ đoạn tin đến nào ngoại trừ một đoạn tin reset khác. Đặc biệt, các gói tin SYN được gửi tới một kết nối không tồn tại sẽ bị từ chối bằng cách này. Nói cách khác, nếu Nmap gửi một yêu cầu TCP với cờ SYN được đặt tới một cổng đã đóng, máy chủ đích sẽ phản hồi bằng một gói tin TCP có cờ RST (Reset) được đặt. Bằng phản hồi này, Nmap có thể xác định được rằng cổng đó đã bị đóng.

```

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/7d52e7f0-199e-4f97-872b-8d407180bdf3)

Tuy vậy còn có 1 khả năng nữa, nếu cổng đó mở nhưng nó nằm sau 1 tường lửa và tưởng lửa này được cấu hình để chặn những gói tin đến thì ta có thể kết luận rằng nếu Nmap gửi một yêu cầu TCP SYN, nhưng không nhận được bất cứ thông tin phản hồi nào thì có thể cổng đó đang được bảo vệ bởi một tường lửa và do đó cổng đó được coi là đã bị chặn (filtered).


## Scan Types SYN Scans

Như các quét TCP, quét SYN (-sS) được sử dụng để quét các cổng TCP của một hoặc nhiều mục tiêu; tuy nhiên, hai loại quét này hoạt động một chút khác nhau. Các quét SYN đôi khi được gọi là các quét "Half-open" hoặc quét "Stealth".

Trong khi quét TCP thực hiện một bắt tay ba bước đầy đủ với mục tiêu, các quét SYN gửi trả lại một gói tin TCP RST sau khi nhận được một gói tin SYN/ACK từ máy chủ (điều này ngăn máy chủ cố gắng lặp lại yêu cầu). Nói cách khác, quá trình quét một cổng mở trông giống như sau đây:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/d0b95813-dc2b-40cf-804a-9b524ebd536d)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/af1395e4-0437-4aaf-9a34-495888cc7b06)

Cơ bản thì nó sẽ có ích hơn cho các kẻ tấn công: 

```
Các scan SYN thường không được lưu vào các ứng dụng đang lắng nghe trên các cổng mở, vì tiêu chuẩn là ghi nhận một kết nối sau khi nó đã được thiết lập hoàn toàn. Điều này phù hợp với ý tưởng về tính ẩn nấp của các scan SYN.

Do không cần phải hoàn thành (và ngắt kết nối từ) một bắt tay ba bước cho mỗi cổng, các scan SYN nhanh hơn đáng kể so với các quét TCP Connect tiêu chuẩn.

```

Tuy vậy cũng có những nhược điểm như: 

```
Các scan SYN đòi hỏi quyền sudo[1] để hoạt động chính xác trên Linux. Điều này vì các scan SYN yêu cầu khả năng tạo ra các gói tin raw (thay vì bắt tay ba bước TCP đầy đủ), đó là một đặc quyền mà chỉ người dùng root mới có mặc định.

Các dịch vụ không ổn định đôi khi bị đánh sập bởi các scan SYN, điều này có thể gây rắc rối nếu một khách hàng cung cấp một môi trường sản xuất cho kiểm tra.

```

Tổng thể thì các scan SYN là các scan mặc định được sử dụng bởi Nmap nếu được chạy với quyền sudo. Nếu chạy mà không có quyền sudo, Nmap sẽ mặc định sử dụng quét TCP Connect.
Ngoài ra thì trường hợp của SYN scan cũng tương tự như TCP scan, nếu một cổng bị đóng thì máy chủ sẽ phản hồi với một gói tin TCP RST. Nếu cổng bị chặn bởi tường lửa thì gói tin TCP SYN sẽ bị loại bỏ hoặc giả mạo với một TCP reset.

## Scan Types UDP Scans

Không giống như 2 loại được đề cập ở trên thì UDP nó là stateless (không trạng thái). Điều này có nghĩa là, thay vì khởi tạo một kết nối với một bắt tay "handshake" hai chiều, các kết nối UDP dựa trên việc gửi các gói tin đến một cổng đích và hy vọng rằng chúng sẽ đi đến được. Điều này làm cho UDP rất oke cho các kết nối phụ thuộc vào tốc độ hơn là chất lượng (ví dụ: chia sẻ video), nhưng sự thiếu sự xác nhận khiến việc quét UDP trở nên khó khăn hơn nhiều và chậm hơn rất nhiều. Thực hiện quét UDP với Nmap là (-sU).

Khi gửi gói tin udp đến cổng, sẽ có 3 phản hồi: open|filter và closed, thông thường thì sẽ kh nhận được phản hồi từ port, và sẽ khó để nhận biết là open|filter, còn nếu muốn xác định đóng thì có thể dùng giao thức icmp với thông báo "port unreachable" để có thể xác định là port đóng. 

## Scan Types NULL, FIN and Xmas

Các scan Null, Fin và Xmas TCP thì ít phổ biến hơn, do vậy ta sẽ đi tìm hiểu cách sử dụng căn bản các loại này thôi. 
Đầu tiên với kiểu NULL, Null Scan: -Sn là kiểu TCP request được gửi đi mà không cần kh có flag nào được set. Theo RFC, máy chủ đích sẽ phản hồi với một gói tin RST nếu cổng đóng:

FIN Scan (-Sf) hoạt động gần giống với quét NULL; tuy nhiên, thay vì gửi một gói tin hoàn toàn trống, một yêu cầu được gửi với cờ FIN (thường được sử dụng để đóng kết nối đang hoạt động). Nếu cổng đóng gói tin RST sẽ được trả về tương tự. 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/bb832fe9-6252-4dc5-a635-5060c3675dac)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/4c623fcf-899d-4bbc-994e-39d9b753865e)

Xmas scan (-sX) gửi một gói tin TCP bị lỗi và mong đợi phản hồi RST cho các cổng đã đóng. Nó được gọi là quét Xmas vì các cờ mà nó đặt (PSH, URG và FIN) tạo ra hình ảnh của một cây thông noel nhấp nháy khi xem dưới dạng bắt gói tin trong Wireshark.

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/749f328e-761a-4755-a88b-b74f7e54f247)

PHản hồi của các kiểu này cho các cổng mở thì cũng giống nhau và rất giống với phản hồi của một UDP scan. Nếu cổng mở thì không có phản hồi cho gói tin bị lỗi này. Các loại quét NULL, FIN và Xmas chỉ xác định các cổng là open | filter, close. Nếu một cổng được xác định là bị lọc với một trong những loại quét này thì thông thường đó là vì mục tiêu đã phản hồi với một gói tin không thể đến được ICMP.

Tóm lại, các loại quét NULL, FIN và Xmas của Nmap được sử dụng do có khả năng vượt qua tường lửa để quét các cổng TCP đích. Với các loại quét này, Nmap gửi các gói tin TCP không chứa cờ SYN, điều này giúp tránh được tường lửa có cấu hình để chặn các gói tin TCP đến các cổng bị chặn.

Tuy nhiên, các tường lửa và hệ thống phát hiện xâm nhập (IDS) hiện đại đã được cấu hình để phát hiện và ngăn chặn các loại quét này. Các giải pháp IDS và tường lửa hiện đại có thể phát hiện các loại quét này bằng cách theo dõi lưu lượng mạng và phân tích các mẫu lưu lượng không bình thường.

Do đó, các loại quét NULL, FIN và Xmas không đảm bảo hoạt động 100% hiệu quả với các hệ thống hiện đại. Để bypass các tường lửa và hệ thống IDS hiện đại, Nmap cung cấp các phương pháp quét khác nhau, bao gồm các loại quét khác như TCP SYN, TCP Connect và các phương pháp tấn công khác như tấn công bằng từ điển (Brute Force) và tấn công bằng mã độc (Malware).


## Scan Types ICMP Network Scanning

Trong kiểm thử bảo mật black box, mục tiêu đầu tiên của chúng ta là thu thập thông tin về cấu trúc mạng - tức là xem xét địa chỉ IP nào chứa các máy chủ hoạt động và địa chỉ nào không.

Một trong những cách để làm điều này là sử dụng Nmap để thực hiện một "ping sweep". Nmap gửi một gói tin ICMP đến mỗi địa chỉ IP có thể có trên mạng được chỉ định. Khi nó nhận được một phản hồi, nó đánh dấu địa chỉ IP đó là có máy chủ hoạt động. Để thực hiện ping sweep, chúng ta sử dụng tùy chọn -sn kết hợp với các phạm vi địa chỉ IP được chỉ định bằng dấu gạch ngang (-) hoặc ký hiệu CIDR. Ví dụ, chúng ta có thể quét mạng 192.168.0.x bằng cách sử dụng lệnh:

> nmap -sn 192.168.0.1-254 hoặc nmap -sn 192.168.0.1/24 (24 bit được sử dụng để định danh phần mạng của địa chỉ IP).


<hr> 


Ta sẽ thực hành 1 số bài lab để hiểu và thực hành được cách dùng. 


## [Easy Peasy](https://tryhackme.com/room/easypeasyctf)<a name='ep'></a>

### 1. Enumeration through Nmap

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/2799abeb-b491-4ed1-b52c-d2d12d60a0ef)

Để scan số port đang hoạt động ta dùng: nmap -p- -vv 10.10.234.133
Nó sẽ mặc định scan 65535 port với kiểu syn Scan, và trả vể cho ta 3 cổng đang hoạt động: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/49fb8e2b-0271-47b6-b219-e82d1a728270)

Để check version thì ta sẽ thêm -sV vào và muốn xem chi tiết thì ta có thể thêm option -vv
Được version của nginx: 1.16.1

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/a3cfe219-f676-4bb6-badd-3ad97b479ffe)

Scan port ở trên ta được port cao nhất là 65524, ta scan: nmap -sV -p 65524 10.10.234.133

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/497f26ce-0bf8-480b-bd21-6824db41fc2e)

> Apache

### 2. Compromising the machine

Ở phần 2 bài cho ta thực hành nhiều tool hơn và cho 1 file easypeasy.txt 
Đầu tiên là dùng gobuster để tìm flag1
Scan với list là rockyou.txt ta được /hidden:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/485ac51d-4b5c-4299-a269-2b01a88658a7)

Truy cập vào ta được: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/176bf480-d0c6-4cca-8fd8-545c4a45d42c)

Nhưng có vẻ cũng không có gì: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/67b9c9b8-fe04-4ced-b3dd-0748ee6923d8)

Ta tiếp tục gobuster tiếp xem có thư mục con nào nữa không, ta được thư mục whatever: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/147006b0-010d-4a51-b0e8-1f7d646e89ba)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/4b8e5548-e24e-4862-9ed5-e23e6c0ed283)

Lần này có trả về thông điệp hidden là ZmxhZ3tmMXJzN19mbDRnfQ== . 
Dễ thấy nó có dạng base64 encode, dùng cmd: **echo -e "ZmxhZ3tmMXJzN19mbDRnfQ==" | base64 -d**  hoặc ném lên cyberchef ta được flag đầu tiên trả về:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/d526224c-6c6e-4dfc-a3bb-35634ceeb901)

Vì không cho dữ kiện gì thêm nên ta sẽ thử khai thác bức ảnh mà ở hidden nó cung cấp cho, có thể nó được giấu thông tin ở trong bức ảnh đó. 
Ta down ảnh về với wget và để phân tích ảnh bên trong ra thì ta có thể dùng steghide và dùng extract để trích xuất thông tin file ảnh với -sf (hay --stegofile) là tùy chọn để chỉ định tên file ảnh. Tuy vậy ta sẽ cần pass ở đây để truy cập vào, nhưng ta không có hint gì cả, vì vậy ta sẽ chuyển sang nhánh tiếp để khai thác tiếp cái khác xem có gì không rồi quay lại sau. 

Ta để ý khi quét nmap ở cổng 65524 ta được trả về thông tin như sau:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/bc20e910-b88a-4db4-a6b7-0f858572a6ae)

Truy cập vào /robots.txt ta được: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/5ea754b8-f21b-4879-b0ca-7a1c66e4cb91)

Dùng hash-identifier để check kiểu hash với : a18672860d0510e5ab6699730763b250

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/b9207b88-078d-4a1f-bc18-20a087695877)

Khả năng cao đây là md5, ta lên tìm kiếm md5 reverse và được:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/344482c0-4f49-4420-81e9-0a48ef8c95e2)


## [Brooklyn Nine Nine](https://tryhackme.com/room/brooklynninenine)<a name='bnn'></a>

## [Net Sec Challenge](https://tryhackme.com/room/netsecchallenge)<a name='nsc'></a>

## [Anonymous](https://tryhackme.com/room/anonymous)<a name='a'></a>
