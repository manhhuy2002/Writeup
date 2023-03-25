## Insecure deserialization - Portswigger
* [1. Modifying serialized objects](#id1)
* [2. Modifying serialized data types](#id2)
* [3. Using application functionality to exploit insecure deserialization](#id3)
* [4. Arbitrary object injection in PHP](#id4)
* [5. Exploiting Java deserialization with Apache Commons](#id5)
* [6. Exploiting PHP deserialization with a pre-built gadget chain](#id6)
* [7. Exploiting Ruby deserialization using a documented gadget chain](#id7)
* [8. Developing a custom gadget chain for Java deserialization](#id8)
* [9. Developing a custom gadget chain for PHP deserialization](#id9)
* [10. Using PHAR deserialization to deploy a custom gadget chain](#id10)


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
Ở đây mình dùng thêm trường message của đối tượng ResponseType hay kiểu sẽ được trả về để xem có thực hiện đổi thành công không: 

![image](https://user-images.githubusercontent.com/104350480/227536084-13fe3447-6d89-4eef-99cc-440daaf21468.png)

Vậy là mình đã đổi thành công, để biết là admin hay administrator thì có thể ra phần đăng nhập thử nếu trùng thì nó sẽ báo lỗi người dùng đã tồn tại hoặc xem source cũng được. Bây giờ mình đăng nhập vào sẽ được như sau: 

![image](https://user-images.githubusercontent.com/104350480/227536990-1ae51663-7721-45c7-9026-74a0f305c3ad.png)


> Flag: HTB{1d0r5_4r3_s1mpl3_4nd_1mp4ctful!!}

## Hack the box - Didactic Octo Paddles

```
You have been hired by the Intergalactic Ministry of Spies to retrieve a powerful relic that is believed to be hidden within the small paddle shop, by the river. You must hack into the paddle shop's system to obtain information on the relic's location. Your ultimate challenge is to shut down the parasitic alien vessels and save humanity from certain destruction by retrieving the relic hidden within the Didactic Octo Paddles shop.

```

Giao diện của bài này: 

![image](https://user-images.githubusercontent.com/104350480/227564014-7e3bfacf-8f81-479a-a639-996a99fda465.png)

Nhìn qua chưa khai thác được gì, mình review source code mình tạo tài khoản và đăng nhập xem được gì:

![image](https://user-images.githubusercontent.com/104350480/227565878-36b50d4a-0713-4f7c-ab50-8966626a9ccb.png)

Có /register mình tạo tài khoản và đăng nhập thì được giao diện mới như sau: 

![image](https://user-images.githubusercontent.com/104350480/227566192-22a109f4-3376-45a5-aa97-136d13f10dba.png)

Tiếp tục đọc source mình được:

![image](https://user-images.githubusercontent.com/104350480/227568837-b76dbb14-439f-4b7f-8e4b-0fea78260091.png)

Có vẻ như nếu mình có thể truy cập và /admin thì có thể exploit SSTI ở đây vì nó đang được render một cách không an toàn. Bây giờ cần phải truy cập được /admin để khai thác được nó. Để ý khi gọi /admin thì nó sẽ gọi 1 hàm trung gian ở đây là  AdminMiddleware trước để xác thực xem mình có quyền vào hay không. Vì vậy phải sang bên  AdminMiddleware.js xem để khai thác tiếp. Source của file  AdminMiddleware.js như sau:

```

const jwt = require("jsonwebtoken");
const { tokenKey } = require("../utils/authorization");
const db = require("../utils/database");

const AdminMiddleware = async (req, res, next) => {
    try {
        const sessionCookie = req.cookies.session;
        if (!sessionCookie) {
            return res.redirect("/login");
        }
        const decoded = jwt.decode(sessionCookie, { complete: true });

        if (decoded.header.alg == 'none') {
            return res.redirect("/login");
        } else if (decoded.header.alg == "HS256") {
            const user = jwt.verify(sessionCookie, tokenKey, {
                algorithms: [decoded.header.alg],
            });
            if (
                !(await db.Users.findOne({
                    where: { id: user.id, username: "admin" },
                }))
            ) {
                return res.status(403).send("You are not an admin");
            }
        } else {
            const user = jwt.verify(sessionCookie, null, {
                algorithms: [decoded.header.alg],
            });
            if (
                !(await db.Users.findOne({
                    where: { id: user.id, username: "admin" },
                }))
            ) {
                return res
                    .status(403)
                    .send({ message: "You are not an admin" });
            }
        }
    } catch (err) {
        return res.redirect("/login");
    }
    next();
};

module.exports = AdminMiddleware;

```
![image](https://user-images.githubusercontent.com/104350480/227573524-4ebc9712-a4f2-4962-9194-dc2111f1632c.png)

Ở file này, nó đang sử dụng jwt để xác thực, với điều kiện kiểm tra ở đây là ở phần header với algorithm không được để là none, nếu để là none trang sẽ tự động redirect mình về /login.
Tiếp theo nếu để alg là HS256 thì nó sử dụng jwt.verify () để giải mã JWT và lấy ra thông tin user và kiểm tra xem user có phải là admin hay không thông qua truy vấn đến database. Nếu user là admin, thì người dùng được phép truy cập vào trang admin, nếu không, sẽ trả về mã lỗi HTTP 403 Forbidden.
Trong trường hợp thuật toán ký được sử dụng khác HS256, đoạn code cũng sử dụng jwt.verify () để giải mã JWT với thuật toán ký đó. Sau đó kiểm tra xem user có phải là admin hay không và trả về mã lỗi 403 Forbidden nếu không phải.

Nhìn qua chỉ có thể đoạn cuối có thể khai thác, ở đây bài chỉ filter alg để là none, mà mình hoàn toàn có thể để là NONE với cùng ý nghĩa là không dùng thuật toán, và vì vậy nó hoàn toàn có thế chui xuống điều kiện cuối cùng. Trước hết xem qua jwt được cấp đã: 

![image](https://user-images.githubusercontent.com/104350480/227574770-9edac5e5-5f2e-4292-b331-f42848c9167b.png)

Giờ mình lấy mã jwt trên và sửa lại một chút, ở đây không có cái isAdmin hay không ở phần payload và tạo tài khoản đầu tiên đã có id = 2, nên có thể đoán là admin ở đây sẽ tương ứng với id = 1 và vì alg là NONE nên ở đây sẽ bỏ luôn signature luôn: 

```
header: eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0 ứng với {"alg":"NONE","typ":"JWT"}
payload: ew0KICAiaWQiOiAxLA0KICAiaWF0IjogMTY3OTY3MTA5MywNCiAgImV4cCI6IDE2Nzk2NzQ2OTMNCn0 ứng với {
  "id": 1,
  "iat": 1679671093,
  "exp": 1679674693
}

Và Jwt truyền vào:

eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0.ew0KICAiaWQiOiAxLA0KICAiaWF0IjogMTY3OTY3MTA5MywNCiAgImV4cCI6IDE2Nzk2NzQ2OTMNCn0.

```

Sau khi truyền vào, mình đã thực hiện việc chuyển hướng trang /admin thành công: 

![image](https://user-images.githubusercontent.com/104350480/227581211-17407459-be6f-4417-8189-6798d17b8d26.png)

Tiếp theo cần khai thác tiếp cái chỗ SSTI vừa nãy. 
Để ý kĩ ảnh trên thì nó render username của mình luôn ở phần dưới, xem lại code: 

![image](https://user-images.githubusercontent.com/104350480/227582180-ff3e9862-184c-4c15-b839-ffc34b068f27.png)

Tức giờ mình có thể thực hiện SSTI vào điểm ${usernames} và chờ render ra. Và đây là dạng Template Injection: JsRender/JsViews

> Refference: https://appcheck-ng.com/template-injection-jsrender-jsviews


Tạo tài khoản đăng kí:

![image](https://user-images.githubusercontent.com/104350480/227586119-88847f4f-062f-47cc-ba2a-5a168574f351.png)

Và đăng nhập: 

![image](https://user-images.githubusercontent.com/104350480/227586266-38b0a2a6-30ab-4bb8-8f52-16a600d2032c.png)

Giờ chỉ cần làm lại các bước thực hiện để bypass jwt như nãy nữa là xong: 

![image](https://user-images.githubusercontent.com/104350480/227586937-cdfb744c-0a66-4ee8-8b35-36a2696feb75.png)


> FLag: HTB{Pr3_C0MP111N6_W17H0U7_P4DD13804rD1N6_5K1115}



# Exploiting insecure deserialization vulnerabilities

## Trong phần này mình sẽ đi qua các phần:
- How to identify insecure deserialization 
- Modifying serialized objects that are expected by the website 
- Passing malicious data into dangerous website functionality 
- Injecting arbitrary object types 
- Chaining method invocations to control the flow of data into dangerous sink gadgets 
- Manually creating your own advanced exploits 
- PHAR deserialization 

### How to identify insecure deserialization 

Để xác định được lỗ hổng insecure deserialization, nhìn chung phải xác định xem dữ liệu được truyền trong web có được serialize hay không. Nếu đã xác định được serailize data, lúc này mình bắt đầu có thể test dựa trên kiến thức về lỗ hổng trên php hoặc java,...

### PHP serialization format

PHP sử dụng một định dạng có thể đọc được với các chữ cái đại diện cho kiểu dữ liệu và số đại diện cho độ dài của mỗi mục. VÍ dụm xem một User obj với các thuộc tính: 
```
$user->name= "carlos";
$user->isLoggedIn = true;

```

Khi được serialized, object này nó sẽ có dạng: 

> O:4:User:2:{s:4:"name":s:6:"carlos"; s:10:"sLoggedIn"b:1;}

trong đó thì Obj User có 2 thuộc tính là name và isLoggedIn và độ dài cũng như giá trị tương ứng của mỗi thuộc tính.

PHương thức serialization trong PHP là serialize() và unserialize(). Nếu đọc source code, ta nên bắt đầu với việc tìm kiếm unseriizalize() ở trong code và kiểm tra chúng.

### Java serialization format

Trong một vài ngôn ngữ, chẳng hạn như java nó sử dụng serialize ở định dạng nhị phân. Điều này làm khó hơn nhiều cho việc độc, nhưng ta vẫn có thể xác định dữ liệu serialized được nếu ta nhận diện được một vài dấu hiệu. Ví dụ, các đối tượng serialized trong java luôn bắt đầu với cùng các bytes, nó được mã hóa nhyw ac ed trong hexa và rO0 trong base64.

Bất kì class nào thực thi interface java.io.Serializable có thể được serialized và deserialized. Nếu ta có source code, để ý đến bất kì đoạn code nào sử dụng phương thức readObject(), cái này có thể được dùng để đọc và deserialize dữ liệu từ InputStream.

### Manipulating serialized objects

Khai thác một số lỗ hổng deserialization có thể được thực hiện thông qua việc thay đổi một thuộc tính trong đối tượng serialized. Ta có thể xác định và phân tích cũng như thêm các luồng truyền vào độc hại vào website thông qua quá trình deserialization. Đây là bước đầu cho việc khai thác deserialization cơ bản.

Nhìn chung, có 2 cáchđể ta có thể xử lí các đối tượng serialized. Ta có thể chỉnh sửa trực tiếp các đối tượng trong mỗi luồng byte của nó, hoặc ta có thể viết một script ngắn với ngôn ngữ tương ứng để tạo và thực hiện việc serialize một obj của chính mình. Cách tiếp cận thứ 2 thường sẽ dễ dàng hơn khi làm việc với các định dạng serialize nhị phân.

### Modifying object attributes

Khi giả mạo (tampering) dữ liệu, miễn là kẻ tấn công có thể giữ lại được một đối tượng serialized hợp lệ, quá trình deserialization sẽ tạo một đối tượng bên máy chủ với các thuộc tính được sửa đổi.

Ví dụ đơn giản ở đây, khi xem một website sử dụng một serialized User obj để chứa dữ liệu về các phiên người dùng trong cookie. Nếu một kẻ tấn công (spotted) phát hiện được đối tượng được serialized này trong http request thì hắn có thể decode nó và tìm thấy byte stream:

> O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}

Ở đây thuộc tính isAdmin có điểm b đại diện cho boolean đang để ở 0, nếu để là 1 thì nó sẽ trở thành true, sau đó ta lại re-encode cái đối tượng này lại và sau đó nó sẽ ghi đè lại trên cookie. Sau đó dữ liệu này sẽ được truyền vào và giúp leo thang đặc quyền.

### [1. Modifying serialized objects](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-objects)<a name="id1"></a>

Bắt qua burpsuite ta được:

![image](https://user-images.githubusercontent.com/104350480/227683071-2130194f-f709-485d-b550-98d7e40e2601.png)

Chỉ cần thay b:1 tương ứng với true sau đó apply lại là thành công.

![image](https://user-images.githubusercontent.com/104350480/227683696-5e74dee3-8535-43e6-901b-c7a543e1f211.png)

Chuyển về method POST thêm /admin/delete và username=carlos nữa là xong.

## Modifying data types

Bài lab trên là một ví dụ cơ bản về việc ta có thể điều chỉnh các giá trị thuộc tính trong các đối tuongj serialized, nhưng đồng thời ta cũng có thể thêm các loại dữ liệt đáng ngờ khác vào.

Logic nền tảng tảng trong php đặc biệt dễ bị tổn thương với kiểu xử lí này bởi hành vi của việc so sánh lỏng lẻo giữa các toán tử là khá thường xuyên. Ví dụ, nếu ta thực hiện một so sánh lỏng lẻo giữa một số và một string, php sẽ cố gắng chuyển đổi chuỗi này thành 1 số nghĩa là 5 == "5" và tương ứng với true. Ngoài ra điều này còn hoạt động với bất kì chuỗi nào mà bắt đầu bằng một số. Trong đó thì php sẽ chuyển đổi toàn bộ chuỗi thành một giá trị só dựa trên bắt đầu của chuỗi đó cũng là một số. Ví dụ 10 == "10 lalallalal lalal" trong php vẫn được coi là 10 == 10.

Điều này còn có thể khai thác một cách lạ hơn là: 0 == "abcd efgh" thì nó vẫn là true.
Bởi vì chuỗi này không có số, nghĩa là 0 chữ số trong chuỗi. Php sẽ coi toàn bộ chuỗi này như một số nguyên không. 

Chẳng hạn việc xử lí logic ở trong chuỗi này:

![image](https://user-images.githubusercontent.com/104350480/227687394-9612a5e2-91a7-44ff-a0d0-6d3ba44e594e.png)

Nó hoàn toàn có thể bị khai thác đơn giản vì đang để so sánh lỏng lẻo, nếu kẻ tấn công điều chỉnh thuộc tính password chứa số nguyên 0 thay vì là 1 string. Miễn là mật khẩu được lưu trữ không bắt đầu là một số thì nó sẽ luôn trả về **true** . Và tất nhiên thì điều này chỉ xảy ra trong quá trình duy trì trạng thái dữ liệu của deserialization. Nếu lấy trực tiếp mật khẩu từ yêu cầu thì nó sẽ bị sai vì lúc này 0 sẽ bị chuyển sang string và kết quả lúc này sẽ là **false**.

## [2. Modifying serialized data types](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-data-types)<a name="id2"></name>

Bài này vẫn được serialize qua cookie:

![image](https://user-images.githubusercontent.com/104350480/227688215-6675d027-f0d6-488c-b457-7974c506f900.png)

Sửa lại một chút quá trình serialize, username thành administrator tương ứng s:13 và để access_token có value là b:1 tương ứng với true:

![image](https://user-images.githubusercontent.com/104350480/227688920-86369dda-ef94-40d7-9337-fbd00643b764.png)

Và tương tự như bài trên ta cũng dùng GET hoặc POST để delete username là carlos.

## Using application functionality

Tiếp đến là việc một web app có thể đang sử dụng các chức năng nguy hiểm mà dùng serialize, ta có thể tận dụng và sửa đội nó để thực hiện các chức năng nguy hiểm . Ví dụ như chức năng "Delete user" trong bài lab sắp tới đây, ảnh hồ sơ của người dùng sẽ bị xóa bằng cách truy cập đường dẫn tệp trong thuộc tính $ user-> Image_Location. Nếu $user được tạo từ một serialized obj, một kẻ tấn công có thể khai thác điều này bằng việc truyền đối tượng đã bị điều chỉnh với 
image_location và đặt thành một đường dẫn tùy ý và chẳng hạn như ở bài lab này sẽ xóa đi user carlos chẳng hạn.

### [3. Using application functionality to exploit insecure deserialization](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-using-application-functionality-to-exploit-insecure-deserialization)

```
This lab uses a serialization-based session mechanism. A certain feature invokes a dangerous method on data provided in a serialized object. To solve the lab, edit the serialized object in the session cookie and use it to delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter

You also have access to a backup account: gregg:rosebud

```

Chẳng hạn khi vào giao diện của bài:

![image](https://user-images.githubusercontent.com/104350480/227693532-911d8687-cfe3-4e69-b566-1203f8617669.png)

Có chứng năng delete, mà giờ xóa nhầm là mất tài khoản luôn nên sẽ thử với tài khoản gregg:rosebud trước. Bài cho phép ta thực hiện xóa người dùng nên ở đây hoàn toàn có thể khai thác điều này bằng việc chỉ cần sửa dổi đường dẫn file, thay vì để đường dẫn avatar như ban đầu thì đổi thành /home/carlos/morale.txt với độ dài là 23 để thỏa mãn yêu cầu đề bài:

![image](https://user-images.githubusercontent.com/104350480/227695375-f98cf76a-d51b-47d5-bb05-76573f21d07f.png)

Vậy là xong bài lab này.

## Magic method: 
Magic method là tập hợp các method đặc biệt mà ta không cần phải gọi ra, mà nó sẽ được tự động thực thi khi có 1 sự kiện tương ứng với nó xảy ra. Các magic methods là tính năng phổ biến trong lập trình hướng đối tượng. 
Các developer thể thêm các magic methods vào 1 class để xác định trước mã nào nên được thực thi khi một sự kiện tương ứng xảy ra. Chính xác là khi nào và tại sao một magic method được gọi từ phương thức này qua phương thức khác. Một trong những ví dụ phổ biến nhất trong PHP là \_\_contruct() , nó sẽ được gọi bất cứ khi nào một đối tượng trong lớp được khởi tạo, cũng tương tự như \_\_inti\_\_ trong python. Thông thường thì các phương thức magic khởi tạo như này sẽ chứa code để khởi tạo các thuộc tính của instance. Tuy nhiên thì các magic methods có thể bị điều chỉnh bởi các dev để thực thi những đoạn code mà ta muốn.

Magic methos được sử dụng rộng rãi và bản thân chúng thì không đại diện cho một lỗ hổng. Nhưng chúng có thể trở nên nguy hiểm nếu dữ liệu đầu vào có thể bị kiểm soát. VÍ dụ, như từ một deserialize obj. Chẳng hạn trong php thì một số hàm magic method thường gặp như \_\_wakeup sẽ tự động được gọi khi một đối tượng thực hiện unserialize,...

Trong java deserialization, điều tương tự được áp dụng với method ObjectInputStream.readObject(), phương thức này được sử dụng để đọc dữ liệu từ byte stream ban đầu và về cơ bản nó hoạt động như một constructor (hàm tạo)  để "re-initializing" (khởi tạo lại) một serialized obj. Tuy nhiên thì Serializeable class có thể khia báo phương thức readObject() của riêng nó như sau:

```
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException
{
    // implementation
}

```
Một readObject() mehtod khai báo  chính xác như cách 1 magic method được gọi trong quá trình deserialization. Điều này cho phép class kiểm soát quá trình deserialization các trường của chính nó chặt chẽ hơn. Đây sẽ là điểm khởi đầu cho các cuộc khai thác cao hơn trong java.


### [3. Injecting arbitrary objects](https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-arbitrary-object-injection-in-php)<a name="id3"></a>

```
This lab uses a serialization-based session mechanism and is vulnerable to arbitrary object injection as a result. To solve the lab, create and inject a malicious serialized object to delete the morale.txt file from Carlos's home directory. You will need to obtain source code access to solve this lab.

You can log in to your own account using the following credentials: wiener:peter

```

Bài này yêu cầu ta phải kiếm được source để khai thác, ctrl U ta được:

![image](https://user-images.githubusercontent.com/104350480/227697292-3f5663cf-fb63-4504-aead-08b03cbc4a95.png)

Thử truy cập vào đường dẫn /libs/CustomTemplate.php thì báo lỗi Not found, để ý thì nó có nói mới được update, vậy ta có thể lấy source thông qua bản backup của nó bằng việc thêm ~ ở cuối và được source như sau: 

![image](https://user-images.githubusercontent.com/104350480/227697438-3ed46225-5c6e-4343-83a8-56322330e8bb.png)

```
<?php

class CustomTemplate {
    private $template_file_path;
    private $lock_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
        $this->lock_file_path = $template_file_path . ".lock";
    }

    private function isTemplateLocked() {
        return file_exists($this->lock_file_path);
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lock_file_path, "") === false) {
                throw new Exception("Could not write to " . $this->lock_file_path);
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }

    function __destruct() {
        // Carlos thought this would be a good idea
        if (file_exists($this->lock_file_path)) {
            unlink($this->lock_file_path);
        }
    }
}

?>

```
Nhìn đoạn code ta có thể khai thác đơn giản như sau: 
Mục tiêu là giờ gọi được \_\_destruct ra để thực hiện unlink cái file mình muốn, mà \_\_destruct được gọi ra khi một đối tượng bị hủy hoặc không được dùng nữa, hoặc là khi kết thúc chương trình. Nên ở đây ta sẽ khởi tạo 1 đối tượng mới là new CustomTemplate để gọi được hàm khởi tạo \_\_construct() và khi gọi được nó ra rồi thì ta sẽ truyền cái link mình muốn vào để 
