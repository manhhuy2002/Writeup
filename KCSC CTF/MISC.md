## MISC challenge

## Git Gud


Bài cho 1 file zip để down về:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/f3fdf205-0321-4662-a932-d365484157a8)

Ngoài 1 file README ra thì bên trong nó còn 1 folder ẩn .git:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/ef69c77e-1e6d-4c22-98c6-5ede3ae2367d)

Như thông thường mình sẽ kiểm tra xem bên trong nó có gì, đầu tiên là với các commit vì thường sẽ khai thác được gì đó, để show nhanh mình dùng **git log --pretty=oneline**:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/327b6f75-26d5-4f7d-acf5-08c65ce489cc)

Bài này thì lúc đầu mình loay hoay lục tung tất cả các commit để khai thác, thì nó trả về thông tin về 1 ảnh là rac.jpg, 1 đường dẫn pastebin nhưng đã bị xóa, và 1 đường dẫn youtube **[khong có dou](https://www.youtube.com/watch?v=9Bl1pxG1N_A)** =)). Mình nghĩ flag có thể được giấu trong ảnh nên mình có thử:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/fb7ed43c-4459-4296-8748-5f4b2cf1e424)

Nhưng mà kh được gì, ngồi phân tích đi phân tích lại kh có khai thác được gì, mình có lên wayback machine để xem liệu đường dẫn pastebin kia có tồn tại từ mấy ngày trước kh thì cũng kh được: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/bee24b0a-08bc-4076-81bc-cd59acc88392)

Vậy có khả năng flag được in trên ảnh trên, mãi sau thì mình xem lại ảnh bằng cách push nó lên github để có thể xem trực quan: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/556fe6d5-35aa-4867-8d0b-3bf59beab34b)

Và đúng ở phần commit upload rac, mình lấy được flag:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/0dda42cb-e5dd-472b-8836-cc12ac9166c5)

> Flag: KCSC{G1t_h1st0Ry_d1v1n9}


## Shackles

```
justatree781 is also a data leak suspect we are looking for. It is reported that he has leaked his confidential information himself that we can exploit and access his account.

```

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/6305d8ea-97c8-4468-b480-c4168fa4aa25)

Bài này hướng đi của mình thì đúng rồi nhưng đoạn cuối làm kh ra nên khá là tiếc. Đầu tiên thì như thông tin đề bài cho thì ta có thể thu thập thông tin dựa vào keyword là justatree781 và data leak suspect. Mình lên một vài mạng xã hội tìm kiếm thử tên này, thì ở twitter có tìm được duy nhất 1 account này:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/a3980d55-fd05-4f71-a763-ee39a1a6b0ae)


Nhìn đoạn mô tả về  **100k for full B company employees data??** và **@justatree781  justatree781 is my nickname** thì có vẻ chính xác đây là người cần tìm rồi. Mình vào link được gắn phía dưới:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/b391fb2c-0185-4aaf-a9f0-b1d04709418d)


Lúc này mình nhìn đường dẫn và nếu mìn bỏ bớt thì sao: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/cf5cfe3c-b2cf-436f-8853-852f30ae52c9)

Ta được tài khoản github của justatree781 và để ý ở đây có 3 đường dẫn: 

Đường dẫn đầu tiên là cái mình vừa mở ở trên, cái thứ 2 và cái thứ 3 thì ở dạng sau: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/d26b6f17-9881-43d4-b568-788148255d40)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/46a33bd9-8165-48d7-941d-5fe249a11575)

Đoạn này nó hint là discord khá nhiều, nhưng mà dựa vào thông tin trên thì chưa khai thác được gì. Hướng đi tiếp của bài này là bỏ từ /raw đổ đi ta sẽ được thêm thông tin:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/b163bcb7-8910-40c9-9eb0-74428a82281c)

Dựa vào thông tin này có thể truy cập vào discord và lấy flag:

> $ curl -sH "Authorization: MTA0OTI2NDEwOTIxODE4NTI0Nw.GYYIn3.fO2pfn9HuvHhfV1IWnfbNzVh9ZSE75XhM5dnLs" https://discordapp.com/api/v6/guilds/1084877131760283658/channels | jq

Mà discord kh truy cập được vào nữa, kh biết có phải bị ai hack kh :((
